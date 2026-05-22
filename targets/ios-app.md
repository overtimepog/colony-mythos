# Target Scaffold: iOS Application (.app / IPA)

**Type:** `ios-app`
**Label:** iOS Application
**Description:** An iOS application bundle running on simulator or device.

## Available Tools

`xcrun simctl`, `ideviceinstaller`, `idevice_id`, `iproxy`, `plutil`, `otool`,
`class-dump`, `strings`, `nm`, `frida`, `objection`, `codesign`

## Recon Steps

- Map the .app bundle: Info.plist, entitlements, embedded frameworks, provisioning
- Audit Info.plist: URL schemes, ATS exceptions, background modes, UIBackgroundModes
- Check entitlements: sandbox, keychain groups, app groups, push notifications
- Find URL scheme / universal link handlers — PRIMARY remote trigger surface
- Map IPC surface: XPC services, Mach ports, CFMessagePort, Darwin notifications
- Audit App Extensions: share extensions, widgets, notification content extensions
- Check for insecure data storage: UserDefaults, Keychain without access controls
- Find WebView use: WKWebView JavaScript bridges, message handlers, URL interception
- Audit native code: otool/class-dump to map ObjC/Swift class hierarchy
- Check for hardcoded secrets in binary strings, plists, asset catalogs
- Map file access: Documents dir, caches, tmp, shared containers (app groups)
- Audit network: NSAppTransportSecurity exceptions, SSL pinning, URLSession config
- Check code signing: entitlements, hardened runtime, library validation

## Oracle Hints

- **behavioral**: Trigger URL schemes with crafted parameters, observe handler behavior
- **differential**: Compare behavior across iOS versions (simulators for 18.5 vs 26.0)
- **crash**: Send malformed IPC messages to XPC services
- **static**: class-dump + grep for dangerous ObjC/Swift patterns
- **behavioral**: Test WebView message handlers with injected payloads
- **differential**: Compare sandbox containment on simulator vs device
- **crash**: Fuzz document/file import handlers with malformed files

## Attack Classes

URL scheme/universal link injection, XPC service abuse, entitlement escape,
WKWebView message handler injection, App Extension attack surface exploitation,
plist/UserDefaults data exposure, Keychain cross-app access, sandbox escape,
code signing bypass, simulator differential bugs, IPC injection,
file parsing bugs, hardcoded credentials.

## Key Research Commands

```bash
# Bundle inspection
plutil -p App.app/Info.plist
codesign -d --entitlements :- App.app 2>/dev/null | plutil -p -

# URL schemes
plutil -p App.app/Info.plist | grep -A10 CFBundleURLTypes
plutil -p App.app/Info.plist | grep -A10 CFBundleURLSchemes

# ATS exceptions
plutil -p App.app/Info.plist | grep -A20 NSAppTransportSecurity

# Binary analysis
otool -L App.app/App           # linked libraries
nm -g App.app/App | head -50   # exported symbols
strings App.app/App | grep -i "http\|password\|secret\|key\|token"

# Dangerous ObjC selectors
strings App.app/App | grep -i "openURL\|handleOpenURL\|userContentController\|evaluateJavaScript"

# Hardcoded secrets
strings App.app/App | grep -iE "(api[_-]?key|secret|token|password|auth)"

# XPC service discovery
find App.app/ -name "*.xpc" -type d

# App Extension discovery
find App.app/PlugIns/ -name "*.appex" -type d
plutil -p App.app/PlugIns/*.appex/Info.plist | grep -E "NSExtensionPointIdentifier\|CFBundleURLSchemes"

# File paths in binary
strings App.app/App | grep -E "^/" | sort -u

# Simulator testing
xcrun simctl list devices
xcrun simctl boot "iPhone 16 Pro"
xcrun simctl install booted App.app
xcrun simctl launch booted com.example.app --args "-craftedParam"
xcrun simctl openurl booted "myapp://inject?payload=test"

# Frida
frida -U -l hook.js com.example.app
```
