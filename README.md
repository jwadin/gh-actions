# gh-actions

Reusable GitHub composite actions shared across my own repos.

## Actions

- [`wireguard-connect`](wireguard-connect/action.yml) — bring up a WireGuard tunnel from a client config and confirm the peer handshake actually succeeds (not just that the interface exists). Optionally fixes single-label hostname resolution (e.g. `mysql`, `redis`) which `systemd-resolved` refuses by default.
- [`wireguard-disconnect`](wireguard-disconnect/action.yml) — bring the tunnel back down. Call with `if: always()`.

## Usage

This repo is **private**. Personal GitHub accounts (not orgs) can't reference another private repo's actions directly via `uses: owner/repo/path@ref` — there's no equivalent of the org-level "share actions internally" setting for personal accounts. The reliable pattern is to check this repo out into a subdirectory with a PAT, then reference the action by local path:

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Check out gh-actions
        uses: actions/checkout@v4
        with:
          repository: jwadin/gh-actions
          ref: v1
          token: ${{ secrets.GH_ACTIONS_REPO_TOKEN }}
          path: .gh-actions

      - name: Connect to WireGuard VPN
        uses: ./.gh-actions/wireguard-connect
        with:
          config: ${{ secrets.WG_CONFIG }}

      # ... your steps that need the tunnel ...

      - name: Disconnect WireGuard
        if: always()
        uses: ./.gh-actions/wireguard-disconnect
```

`GH_ACTIONS_REPO_TOKEN` is a fine-grained PAT with **read-only "Contents" access scoped to just this repo**, added as a secret in whichever repo is consuming these actions. It only needs to read this repo's contents — nothing else.

## Versioning

Tagged releases (`v1`, `v2`, ...) so consumers can pin to a stable version rather than `main`. Bump the major tag when making a breaking change to an action's inputs/outputs.
