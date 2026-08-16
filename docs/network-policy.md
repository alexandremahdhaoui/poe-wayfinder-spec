# Network policy

**One host. `www.pathofexile.com`. Everything else is refused.**

## What the reference does

Awakened PoE Trade funnels every request through an Electron session handler
and destroys anything not in a hardcoded array. Exiled Exchange 2 kept that
design and added hosts to the array.

Two problems. The array is code and not config, so a user cannot see it or
change it. And the fork added `api.exiledexchange2.dev`, a server run by the
fork maintainer.

## What we do

The allowlist is config. Three keys control it.

| Key | Default | Effect |
|---|---|---|
| `network_enabled` | `true` | `false` fails every request before a socket opens |
| `block_unlisted_hosts` | `true` | `false` allows any host |
| `allowed_hosts` | `www.pathofexile.com` | comma separated |

Each is settable by flag, by env and by config file. Flag beats env. Env beats
default.

## Hosts we removed and why

| Host | In the reference | Why we dropped it |
|---|---|---|
| `api.exiledexchange2.dev` | yes | a private third party server |
| `pathofexile.tw` | yes | not English |
| `poe.game.daum.net` | yes | not English |
| `poe.game.qq.com` | yes | not English |
| `ru.pathofexile.com` | yes | not English |
| `web.poe.garena.tw` | yes | not English |

Adding any of them back means setting `allowed_hosts`. Nothing in the code
knows their names.

## Where the check lives

`poe-wayfinder-app/src/adapter/http_adapter.rs`. It is the only place in the
project that builds an HTTP client. Every other adapter takes it as a
dependency.

That is deliberate. A single choke point can be audited in one read.

## The rate limiter

Separate from the allowlist and not optional. GGG bans accounts for rate limit
violations.

The trade API returns `x-rate-limit-rules` naming its active rules, then one
header per rule holding `max:window:penalty` triplets. The client must mirror
those limiters exactly, including creating and destroying them when the server
changes its mind.

`api_latency_seconds` pads every window because our clock is not the server's
clock. Default is 2.
