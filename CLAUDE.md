# CLAUDE.md — poe-wayfinder-spec

Read `~/.claude/CLAUDE.md` then `../CLAUDE.md`. Both apply.

## What this repo is

Declarations. No code and no build output.

`spec/poe-wayfinder.v1.yaml` is the source of truth for every config key. If a
value can be set by a user it is declared here and nowhere else.

## The rule

**A hardcoded URL or tunable is a bug.** The whole point of this repo is that
the reference hardcodes both.

Before adding a constant to `poe-wayfinder-app`, ask whether a user would ever
want to change it. If yes it belongs here.

## Duplication across binaries is intended

The six network keys repeat in all three binaries. That is not a mistake.

`golden-configgen` emits one config struct per binary. A binary that cannot
reach the network should not carry a `poesessid` field. Sharing the key list
would give every binary every key.

## Type limits

`golden-configgen` supports `string`, `int`, `bool` and `duration`. There is
no list type, so `allowed_hosts` is a comma separated string that
`http_adapter` splits.

Adding a real list type means changing four emitters in `golden-configgen`.
Worth doing later. Not worth blocking on.

## Validate

```sh
forge test-all
```

The `validate` stage runs `golden-configgen` in dry run mode. It catches an
unknown type or a missing name before any consumer builds.
