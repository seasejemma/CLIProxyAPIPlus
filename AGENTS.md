# AGENTS.md - Hardened Fork Customizations

This branch contains hardened Docker build customizations for the CLIProxyAPIPlus fork.

## Overview

Hardened fork of [router-for-me/CLIProxyAPIPlus](https://github.com/router-for-me/CLIProxyAPIPlus) with:
- Distroless base image (gcr.io/distroless/static:nonroot)
- Multi-arch builds (amd64/arm64)
- Automated upstream sync
- GHCR publishing

## Image

```bash
docker pull ghcr.io/seasejemma/cliproxyapiplus:latest
docker pull ghcr.io/seasejemma/cliproxyapiplus:<version>
```

## Custom Files

| File | Purpose |
|------|---------|
| `Dockerfile.hardened` | Multi-stage build using distroless runtime |
| `.github/workflows/ghcr-hardened.yml` | Build workflow for GHCR |
| `.github/workflows/sync-upstream.yml` | Upstream sync (every 30 min) |
| `docker-compose.override.yml` | Local customizations (cpa, webproxy) |
| `HARDENED.md` | Fork documentation |

## Workflows

### ghcr-hardened.yml
- **Triggers**: Tag push `v*`, manual dispatch with tag input
- **Actions**: Build multi-arch image, push to GHCR, create GitHub Release
- **Base**: `golang:1.24-alpine` (build) → `gcr.io/distroless/static:nonroot` (runtime)

### sync-upstream.yml
- **Triggers**: Cron `*/30 * * * *`, manual dispatch
- **Actions**: 
  1. Merge upstream/main
  2. Remove upstream workflows (docker-image.yml, release.yaml, etc.)
  3. Push new tags
  4. Check if image exists in GHCR
  5. Trigger build if image missing

## Manual Commands

```bash
# Trigger upstream sync
gh workflow run sync-upstream.yml -R seasejemma/CLIProxyAPIPlus

# Trigger build for specific tag
gh workflow run ghcr-hardened.yml -R seasejemma/CLIProxyAPIPlus -f tag=v1.0.0

# Create release manually
gh release create v1.0.0 -R seasejemma/CLIProxyAPIPlus --title "Release 1.0.0" --notes "Hardened build"
```

## Backup Locations

- **Branch**: `hardened-customizations` (this branch)
- **Local**: `/Users/liuwen/work/nexus/safekeep/cliproxyapiplus-hardened/`

## Git Identity

```
Username: jemma
Email: jemma@jane.doe
```

Configured via `~/.gitconfig-github-seasejemma` with `includeIf` pattern.

## Dockerfile.hardened Structure

```dockerfile
# Build stage: golang:1.24-alpine
# - Compiles Go binary with CGO_ENABLED=0
# - Injects version, commit, build date via ldflags

# Runtime stage: gcr.io/distroless/static:nonroot
# - No shell, minimal attack surface
# - Runs as nonroot user (65532)
# - Only contains the binary and config
```
