# Target Scaffold: Source Code Repository

**Type:** `source-repo`
**Label:** Source Code Repository
**Description:** A git repository containing source code. Can be any language.

## Available Tools

`grep`, `git log`, `git diff`, `git blame`, `wc -l`, `find`, `ast-grep`, `semgrep`

## Recon Steps

- Map the directory structure: identify entry points, library code, test code
- Find recently changed files (fragile code): `git log --oneline -40`
- Find reverts (highest priority): `git log --grep='Revert\|Reland' -20`
- Find security-sensitive patterns: auth, crypto, parsing, serialization, IPC
- Map the build system: how is this compiled? what flags?
- Identify trust boundaries: where does external input enter the system?

## Oracle Hints

- **differential**: Compile with different optimization levels, compare behavior
- **crash**: Run with ASAN/UBSAN/Valgrind
- **static**: Pattern-match for known vulnerability signatures
- **behavioral**: Compare with reference implementation

## Attack Classes

Memory corruption (buffer overflow, use-after-free, double-free, type confusion),
injection (command, SQL, code, template), authentication bypass, authorization flaws
(IDOR, privilege escalation), cryptographic weaknesses, race conditions (TOCTOU),
deserialization attacks, path traversal, XXE, SSRF.

## Key Research Commands

```bash
# Fragile code (recent changes)
git log --oneline -40

# Reverts = highest priority targets
git log --oneline --grep="Revert\|Reland" -20

# Security-sensitive commits
git log --oneline --grep="fix\|bug\|crash\|CVE\|security\|overflow\|inject\|bypass" -30

# Map entry points
grep -rn "main\|handle\|process\|parse\|deserialize\|decode" --include="*.c" --include="*.cpp" --include="*.py" --include="*.js"

# Dangerous function calls
grep -rn "strcpy\|strcat\|sprintf\|gets\|system\|exec\|eval\|popen" --include="*.c" --include="*.cpp"

# Auth and crypto
grep -rn "auth\|token\|jwt\|session\|password\|hash\|encrypt\|decrypt\|sign" --include="*.py" --include="*.js" --include="*.go"

# File operations (path traversal risk)
grep -rn "open\|readFile\|writeFile\|mkdir\|unlink" --include="*.py" --include="*.js" --include="*.go"
```
