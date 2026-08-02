# poe-trader-spec

The single source of truth for poe-trader config and network policy.

Nothing here is code. Code is generated from it.

## Contents

| Path | Holds |
|---|---|
| `spec/poe-trader.v1.yaml` | every config key of every binary |
| `docs/network-policy.md` | which hosts the app may reach and why |
| `hack/resolve-spec.sh` | pulls this spec into a consumer's build |

## How consumers use it

`poe-trader-app` resolves this file at build time, then runs
`golden-configgen` over it to emit `zz_generated.config.rs`.

```sh
sh ../poe-trader-spec/hack/resolve-spec.sh \
  github.com/alexandremahdhaoui/poe-trader-spec \
  spec/poe-trader.v1.yaml .forge/spec-cache
```

The resolver checks the local workspace first, then a pinned remote tag.

## Changing a config key

Edit the yaml. Regenerate. Never hand edit a `zz_generated` file.

Adding a key to one binary does not affect the others. Each binary declares
its own key list, so `poe-trader-datagen` never grows a hotkey setting.
