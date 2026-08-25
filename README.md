# gh-actions

Reusable GitHub composite actions shared across my own repos.

## Actions

- [`wireguard-connect`](wireguard-connect/action.yml) — bring up a WireGuard tunnel from a client config and confirm the peer handshake actually succeeds (not just that the interface exists). Optionally fixes single-label hostname resolution (e.g. `mysql`, `redis`) which `systemd-resolved` refuses by default.
- [`wireguard-disconnect`](wireguard-disconnect/action.yml) — bring the tunnel back down. Call with `if: always()`.

## Usage

This repo is public, so any repo can reference these actions directly by tag — no checkout or token needed:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Connect to WireGuard VPN
        uses: jwadin/gh-actions/wireguard-connect@v1
        with:
          config: ${{ secrets.WG_CONFIG }}

      # ... your steps that need the tunnel ...

      - name: Disconnect WireGuard
        if: always()
        uses: jwadin/gh-actions/wireguard-disconnect@v1
```

## Versioning

Tagged releases (`v1`, `v2`, ...) so consumers can pin to a stable version rather than `main`. Bump the major tag when making a breaking change to an action's inputs/outputs.
