# Target Scaffold: Android Application (APK/AAB)

**Type:** `android-app`
**Label:** Android Application
**Description:** An Android app package (.apk or .aab) running on emulator or device.

## Available Tools

`adb`, `apktool`, `jadx`, `aapt`, `dumpsys`, `strings`, `nm`, `frida`, `objection`, `drozer`

## Recon Steps

- Decompile APK: `apktool d` / `jadx` → map Java/Kotlin + native (.so) code
- Audit `AndroidManifest.xml`: exported components, permissions, intent filters
- Map IPC surface: exported Activities, Services, BroadcastReceivers, ContentProviders
- Check for `debuggable` flag, `backup` enabled, cleartext traffic allowed
- Find WebView usage: JavaScript bridges, `addJavascriptInterface`, file access
- Audit deep-link / URL scheme handlers — PRIMARY remote trigger surface
- Check native libraries (.so): exported JNI functions, unsafe memcpy/strcpy
- Inspect content providers: SQL injection, path traversal in `query()`/`openFile()`
- Map permission model: custom permissions, protection levels, signature checks
- Check for hardcoded secrets: API keys, tokens, crypto material in dex/resources
- Find file access patterns: external storage, shared preferences, internal dirs
- Audit network: TLS pinning, cert validation, custom trust managers

## Oracle Hints

- **crash**: Send malformed Intents to exported components (`am start`/`am broadcast`)
- **differential**: Compare behavior across API levels (simulate on API 33+ vs older)
- **behavioral**: Test content providers with SQL injection / path traversal payloads
- **static**: jadx grep for dangerous patterns (`addJavascriptInterface`, `Runtime.exec`)
- **crash**: Fuzz native libraries with malformed inputs via JNI boundary
- **differential**: Compare same app on emulator vs physical device

## Attack Classes

Exported component abuse (Activity/Services/BroadcastReceiver hijacking),
Intent injection, deep-link/URL scheme hijacking, ContentProvider SQL injection,
ContentProvider path traversal, WebView JavaScript bridge injection,
addJavascriptInterface RCE, native library buffer overflow via JNI,
hardcoded secrets, TLS/SSL bypass (custom TrustManager), permission bypass,
Intent redirection, tapjacking, task hijacking, WebView file access abuse.

## Key Research Commands

```bash
# Decompile
apktool d app.apk -o /tmp/apk-decompiled
jadx -d /tmp/jadx-output app.apk

# Manifest analysis
aapt dump badging app.apk
grep -E 'exported="true"' /tmp/apk-decompiled/AndroidManifest.xml
grep -E 'android:debuggable|android:allowBackup|android:usesCleartextTraffic' /tmp/apk-decompiled/AndroidManifest.xml

# Find deep-link handlers
grep -E '<data.*android:scheme|<data.*android:host' /tmp/apk-decompiled/AndroidManifest.xml

# Find exported Components
grep -B2 -A10 'exported="true"' /tmp/apk-decompiled/AndroidManifest.xml

# WebView danger
grep -rn "addJavascriptInterface\|setJavaScriptEnabled\|setAllowFileAccess" /tmp/jadx-output/
grep -rn "loadUrl\|evaluateJavascript" /tmp/jadx-output/

# Dangerous APIs in decompiled Java
grep -rn "Runtime.exec\|ProcessBuilder\|exec(" /tmp/jadx-output/
grep -rn "openFileOutput\|getExternalStorage\|getSharedPreferences" /tmp/jadx-output/
grep -rn "SecretKeySpec\|Cipher\|MessageDigest" /tmp/jadx-output/

# Native library inspection
nm -gD path/to/lib/*.so | grep -i "Java_\|JNI_"
strings path/to/lib/*.so | grep -i "http\|password\|secret\|key"

# Runtime testing (via adb)
adb shell am start -n com.example/.ExportedActivity -e payload "test"
adb shell content query --uri content://com.example.provider/
adb shell am broadcast -n com.example/.Receiver -e data "test"
```
