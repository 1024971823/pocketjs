# PocketJS on RT-Thread / Edgi-Talk

Host scaffold for PocketJS on the Edgi-Talk board (Infineon PSoC E84, Cortex-M55)
running RT-Thread.

This directory mirrors the intent of [`hosts/esp-idf`](../esp-idf): product
firmware owns tasks, input, display, and storage. PocketJS is integrated as the
UI runtime, not as a vendored board support package.

## Honesty / P0 status

- **Reuses** the ESP-IDF host C components under
  `hosts/esp-idf/components/{package,guest,ui_*,render_rgb565}` rather than
  duplicating them yet.
- **Rust** UI / render crates are rebuilt for M55
  (`thumbv8m.main-none-eabihf`). On this board HyperRAM ≈ SPIRAM; ESP-specific
  shims live in the Edgi overlay, not here.
- **Product owns** LCD, touch, Wi-Fi, and BT (same philosophy as the ESP host:
  PocketJS is the UI guest, not the BSP).
- **App** for the Beat Dash smoke UI lives in
  [`apps/edgitalk-m55-smoke/`](../../apps/edgitalk-m55-smoke/) (synced sources +
  assets; this fork is SoT for that tree).
- **Native C glue** under [`native/`](native/) is still TBD (P1) — port from the
  overlay `applications/pocketjs` when ready.
- **`pocket.host.json`** still declares `platform: "esp-idf"` in P0 (schema
  borrow). Track a real RT-Thread host id/schema for P1; do not silently keep
  the lie without a tracking issue.

## What lives here

- Integration notes for the RT-Thread / Edgi-Talk host.
- A native glue placeholder under `native/` (C host glue is still TBD).
- Portable build notes in [`docs/build.md`](docs/build.md).

## What does not live here

BSP patches and Bluetooth firmware are not vendored in this repository. They stay in:

- [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)
  — Edgi overlay, including `applications/pocketjs` (the C host glue to port
  into `native/`) and
  [`projects/Edgi_Talk_M55_PocketJS/docs/`](https://github.com/1024971823/PocketJS_for_Edgi-Talk/tree/main/projects/Edgi_Talk_M55_PocketJS/docs)
  (compile / flash / onboard use).
- The RT-Thread BSP for Edgi-Talk / PSoC E84.

## Upstream

This fork tracks [pocket-nexus/pocketjs](https://github.com/pocket-nexus/pocketjs).
Edgi-Talk host work stays on `host/rt-thread-edgitalk` until it is ready to
propose upstream.
