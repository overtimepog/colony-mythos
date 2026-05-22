# Target Scaffold: Binary Executable

**Type:** `binary-executable`
**Label:** Binary Executable (macOS/Windows/Linux)
**Description:** A compiled binary or .app bundle (non-Electron).

## Available Tools

`nm`, `otool`, `strings`, `codesign`, `lldb`, `hopper`, `ghidra_headless`

## Recon Steps

- Map the binary: symbols, linked libraries, entry points
- Check code signing and entitlements
- Find XPC services, Mach ports, named pipes
- Audit file access, network access, process spawning
- Check for hardcoded secrets in strings
- Map the attack surface: IPC, file parsing, network protocol parsing
- Identify dangerous imports: `system`, `execve`, `popen`, `dlopen`

## Oracle Hints

- **differential**: Compare with different OS versions or compile flags
- **crash**: Fuzz with AFL/libFuzzer
- **behavioral**: Test with different entitlements/sandbox profiles
- **static**: Pattern-match for unsafe API calls in disassembly

## Attack Classes

Buffer overflow, format string, integer overflow/underflow, use-after-free,
TOCTOU race conditions, command injection via subprocess, code signing bypass,
DLL/dylib hijacking, XPC service abuse (macOS), entitlement escape,
IPC message injection, file parsing bugs, hardcoded credentials.

## Key Research Commands

```bash
# Basic triage
file ./binary
otool -L ./binary       # macOS: linked libraries
nm -g ./binary          # exported symbols
strings ./binary | head -100

# Dangerous imports
nm -g ./binary | grep -i "system\|exec\|popen\|fork\|dlopen\|mmap\|mprotect"

# Hardcoded secrets
strings ./binary | grep -i "password\|secret\|token\|key\|api\|auth\|http[s]*://"

# Entitlements (macOS)
codesign -d --entitlements :- ./binary 2>/dev/null

# File/network operations
strings ./binary | grep -E "^/" | sort -u       # file paths
strings ./binary | grep -E "^[a-z]+://" | sort -u  # URLs

# Mach ports / XPC (macOS)
strings ./binary | grep -i "xpc\|mach_port\|bootstrap"

# Command-line parsing (injection surface)
strings ./binary | grep -i "argv\|argc\|getopt\|flag\|command\|--"
```
