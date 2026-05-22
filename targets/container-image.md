# Target Scaffold: Container Image

**Type:** `container-image`
**Label:** Container Image
**Description:** A Docker/OCI container image.

## Available Tools

`docker`, `skopeo`, `dive`, `trivy`, `grype`, `find`, `ls -la`, `ps`, `ss`, `capsh`

## Recon Steps

- Map the filesystem: what's installed? what runs as entrypoint?
- Sweep for setuid/setgid binaries: `find / -perm -4000 -o -perm -2000 2>/dev/null`
- Map the /proc surface: what kernel interfaces are exposed?
- Check running services and their configs
- Audit capability sets: `capsh --print`
- Check for writable paths, temp file races
- Map network exposure: what ports listen? on what interfaces?
- Check for hardcoded secrets in environment, configs, scripts
- Audit the Dockerfile: multi-stage build gaps, COPY --from, HEALTHCHECK

## Oracle Hints

- **differential**: Compare behavior across different base images
- **crash**: Run with kernel ASAN/syzkaller
- **behavioral**: Run with different seccomp profiles
- **static**: trivy/grype vulnerability scanning

## Attack Classes

Container escape (privileged mode, host PID namespace, Docker socket mount),
capability abuse (CAP_SYS_ADMIN, CAP_SYS_PTRACE, CAP_NET_RAW), setuid binary exploitation,
/proc filesystem abuse (cgroup release_agent, core_pattern), volume mount escape
(/var/run/docker.sock exposure), network namespace traversal, seccomp bypass,
kernel exploit from container context, service misconfiguration, hardcoded secrets.

## Key Research Commands

```bash
# Pull and inspect image
docker pull target:latest
docker inspect target:latest
docker history --no-trunc target:latest

# Deep inspection with dive
dive target:latest

# Vulnerability scanning
trivy image target:latest
grype target:latest

# Run a shell in the container
docker run --rm -it --entrypoint /bin/sh target:latest

# Inside container — privilege audit
capsh --print
cat /proc/1/status | grep Cap
find / -perm -4000 -o -perm -2000 2>/dev/null

# Inside container — service audit
ps aux
ss -tlnp
cat /etc/passwd /etc/shadow 2>/dev/null

# Inside container — writable paths
find / -writable -type d 2>/dev/null | grep -v '^/proc\|^/sys'

# Inside container — Docker socket check
ls -la /var/run/docker.sock 2>/dev/null

# Inside container — cgroup escape probe
ls -la /sys/fs/cgroup/
cat /proc/1/cgroup
```
