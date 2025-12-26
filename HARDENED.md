# CLIProxyAPIPlus (Hardened Fork)

Hardened fork of [router-for-me/CLIProxyAPIPlus](https://github.com/router-for-me/CLIProxyAPIPlus) with distroless runtime.

## Image

```
ghcr.io/seasejemma/cliproxyapiplus:latest
ghcr.io/seasejemma/cliproxyapiplus:<version>
```

## Workflows

| Workflow | Trigger | Purpose |
|----------|---------|---------|
| `ghcr-hardened.yml` | Tag push `v*`, manual | Build multi-arch image to GHCR |
| `sync-upstream.yml` | Every 30 min, manual | Sync from upstream, push new tags |

## Manual Triggers

```bash
# Trigger upstream sync
gh workflow run sync-upstream.yml --repo seasejemma/CLIProxyAPIPlus

# Trigger build with custom tag
gh workflow run ghcr-hardened.yml --repo seasejemma/CLIProxyAPIPlus -f tag=dev
```

## Key Files (Custom)

- `Dockerfile.hardened` - Distroless-based hardened image
- `.github/workflows/ghcr-hardened.yml` - GHCR build workflow
- `.github/workflows/sync-upstream.yml` - Upstream sync workflow

## Topics

`tokeninfra`, `adt`
