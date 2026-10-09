# PocketJS on RT-Thread / Edgi-Talk

Host scaffold for PocketJS on the Edgi-Talk board (Infineon PSoC E84, Cortex-M55) running RT-Thread.

This directory mirrors the intent of [`hosts/esp-idf`](../esp-idf): product firmware owns tasks, input, display, and storage. PocketJS is integrated as the UI runtime, not as a vendored board support package.

## What lives here

- Integration notes for the RT-Thread / Edgi-Talk host.
- A native glue placeholder under `native/` (C host glue is still TBD).
- Stub build notes in [`docs/build.md`](docs/build.md).

## What does not live here

BSP patches and Bluetooth firmware are not vendored in this repository. They stay in:

- [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk) — Edgi overlay, including `applications/pocketjs` (the C host glue to port into `native/`).
- The RT-Thread BSP for Edgi-Talk / PSoC E84.

## Upstream

This fork tracks [pocket-nexus/pocketjs](https://github.com/pocket-nexus/pocketjs). Edgi-Talk host work stays on `host/rt-thread-edgitalk` until it is ready to propose upstream.
