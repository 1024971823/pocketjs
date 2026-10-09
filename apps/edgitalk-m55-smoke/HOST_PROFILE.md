# Host profile note — `edgitalk-m55`

Canonical file: [`pocket.host.json`](./pocket.host.json).

## Current values (do not invent a fake schema)

| Field | Value |
| --- | --- |
| `$schema` | `https://pocketjs.dev/schema/pocket-idf-host-1.json` |
| `platform` | `"esp-idf"` |
| `id` | `edgitalk-m55` |

This is an intentional **borrow** of the ESP-IDF host admission shape so the
Pocket CLI and package `PHST` checks work on RT-Thread / Edgi-Talk today. The
firmware is **not** ESP-IDF.

## Tracking

Keep `platform: "esp-idf"` until a real RT-Thread host schema exists
(working name: `pocket-rtt-host-1` / `platform: "rt-thread"`).

**Issue:** https://github.com/1024971823/pocketjs/issues/2

Host tree: [`hosts/rt-thread-edgitalk/`](../../hosts/rt-thread-edgitalk/).
