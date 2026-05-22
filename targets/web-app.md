# Target Scaffold: Web Application

**Type:** `web-app`
**Label:** Web Application
**Description:** A live web application accessible via URL.

## Available Tools

`curl`, `burp`, `ffuf`, `nuclei`, `sqlmap`, `browser devtools`, `subfinder`, `httpx`

## Recon Steps

- Map the attack surface: endpoints, parameters, auth flows, APIs
- Enumerate subdomains and vhosts: `subfinder -d domain.com`
- Check technology stack: what frameworks, what versions? `curl -sI https://target.com`
- Map auth: sessions, JWTs, OAuth, API keys — how does auth actually work?
- Find file upload, import, export, and parsing endpoints
- Check CORS, CSP, and security headers
- Map the API: REST, GraphQL, WebSocket endpoints
- Spider the application: `ffuf -w wordlist.txt -u https://target.com/FUZZ`
- Check for exposed .git, .env, backup files, debug endpoints

## Oracle Hints

- **differential**: Compare behavior with different user roles (admin vs user vs unauthenticated)
- **crash**: Send malformed payloads, oversized requests, Unicode/encoding edge cases
- **behavioral**: Test race conditions with parallel requests, test TOCTOU
- **static**: nuclei/ffuf automated scanning

## Attack Classes

Injection (SQLi, NoSQLi, command injection, template injection/SSTI, LDAP, XPath),
XSS (reflected, stored, DOM-based), CSRF, SSRF, authentication flaws (JWT manipulation,
session fixation, OAuth misconfig), authorization flaws (IDOR, privilege escalation),
path traversal, file inclusion (LFI/RFI), deserialization attacks, XXE,
business logic flaws, race conditions, CORS misconfiguration, open redirect,
subdomain takeover, information disclosure.

## Key Research Commands

```bash
# Technology fingerprint
curl -sI https://target.com | grep -i "server\|x-powered\|set-cookie"

# Subdomain enumeration
subfinder -d target.com | httpx -status-code -title

# Endpoint discovery
ffuf -w ~/wordlists/common.txt -u https://target.com/FUZZ -mc 200,301,302,403

# Parameter discovery
ffuf -w ~/wordlists/params.txt -u https://target.com/page?FUZZ=test -mc 200

# Check for exposed sensitive files
curl -s https://target.com/.git/config
curl -s https://target.com/.env
curl -s https://target.com/robots.txt
curl -s https://target.com/sitemap.xml

# API discovery
curl -s https://target.com/api/ | jq .
ffuf -w ~/wordlists/api.txt -u https://target.com/api/FUZZ

# GraphQL introspection
curl -s https://target.com/graphql -H "Content-Type: application/json" -d '{"query":"{__schema{types{name}}}"}'
```
