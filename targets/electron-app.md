# Target Scaffold: Electron Desktop Application

**Type:** `electron-app`
**Label:** Electron Desktop Application
**Description:** A packaged Electron .app bundle (macOS) or equivalent.

## Available Tools

`npx asar`, `codesign`, `strings`, `grep`, `plutil`, `defaults read`, `ps aux`

## Recon Steps

- Map the app structure: main process, renderer, preload, ASAR, helpers
- Extract and audit the ASAR archive: `npx asar extract app.asar /tmp/asar-contents`
- Check for `nodeIntegration`, `contextIsolation`, `sandbox` settings in package.json
- Find IPC channels: what does main process expose to renderers? `ipcMain.handle`, `ipcMain.on`
- Check entitlements: what OS permissions does the app have? `codesign -d --entitlements :-`
- Find URL schemes / deep-link handlers (BEST remote trigger surface)
- Check for MCP servers, plugin systems, extension points
- Audit data stores: where are credentials/tokens stored? `find ~/Library/Application\ Support/<app>/`
- Check running processes for `--no-sandbox`, `--inspect` flags
- Map the preload script: what bridge APIs does it expose?

## Oracle Hints

- **static**: grep for dangerous patterns in extracted JS: `eval`, `Function()`, `child_process.exec`, `shell.openPath`
- **behavioral**: Register custom URL scheme, trigger deep-link with crafted payload
- **differential**: Compare behavior with and without sandbox (`--no-sandbox`)
- **crash**: Send malformed IPC messages, oversized inputs

## Attack Classes

Node.js integration abuse (child_process, fs, net), IPC channel injection, contextBridge bypass,
deep-link hijacking (URL scheme injection), webview sandbox escape, preload script exploitation,
ASAR integrity bypass, MCP/tool execution injection, protocol handler abuse, prototype pollution,
XSS in renderer (leading to nodeIntegration RCE), local file disclosure, credential theft.

## Key Research Commands

```bash
# Extract ASAR
npx asar extract /Applications/App.app/Contents/Resources/app.asar /tmp/asar-dump

# Find dangerous patterns in extracted JS
grep -rn "child_process\|require('child_process')\|\.exec\|spawn\|fork" /tmp/asar-dump/
grep -rn "shell\.openPath\|shell\.openExternal\|shell\.openItem" /tmp/asar-dump/
grep -rn "ipcMain\.\|ipcRenderer\." /tmp/asar-dump/
grep -rn "nodeIntegration\|contextIsolation\|sandbox" /tmp/asar-dump/
grep -rn "eval\|Function(" /tmp/asar-dump/
grep -rn "webContents\|BrowserWindow\|new BrowserView" /tmp/asar-dump/

# Check entitlements
codesign -d --entitlements :- /Applications/App.app 2>/dev/null | plutil -p -

# Find URL schemes
plutil -p /Applications/App.app/Contents/Info.plist | grep -A5 CFBundleURLSchemes

# Check for command-line flags in running process
ps aux | grep -i "no-sandbox\|inspect\|remote-debugging"

# Find data stores
find ~/Library/Application\ Support/ -maxdepth 2 -name "*.db" -o -name "*.sqlite" -o -name "*.json"
```
