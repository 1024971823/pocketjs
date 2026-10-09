# PocketJS on RT-Thread / Edgi-Talk

Host for PocketJS on the Edgi-Talk board (Infineon PSoC E84, Cortex-M55)
running RT-Thread.

This directory mirrors the intent of [`hosts/esp-idf`](../esp-idf): product
firmware owns tasks, input, display, and storage. PocketJS is integrated as the
UI runtime, not as a vendored board support package.

## Status (P1)

- **Reuses** the ESP-IDF host C components under
  `hosts/esp-idf/components/{package,guest,ui_*,render_rgb565}` (listed by
  [`native/SConscript`](native/SConscript)).
- **Rust** UI / render crates rebuild for M55 (`thumbv8m.main-none-eabihf`).
  HyperRAM ≈ SPIRAM via [`native/pocketjs_rt_compat.c`](native/pocketjs_rt_compat.c).
- **Native glue** under [`native/`](native/): ESP→RT shims, QuickJS compat,
  portable host loop with **board hooks** (`pocketjs_host_board.h`), embed
  pattern, SCons fragment.
- **App** SoT: [`apps/edgitalk-m55-smoke/`](../../apps/edgitalk-m55-smoke/).
- **Product owns** LCD, touch, Wi-Fi, and BT (same philosophy as the ESP host).

### Host identity (`platform: "esp-idf"`)

`apps/edgitalk-m55-smoke/pocket.host.json` still declares `platform: "esp-idf"`
and the `pocket-idf-host-1` schema so CLI admission works. That is an intentional
borrow, **not** a claim the firmware is ESP-IDF.

Tracking: **[#2 — replace platform esp-idf with rt-thread / pocket-rtt-host-1](https://github.com/1024971823/pocketjs/issues/2)**.
Do not invent a fake schema URL; keep `platform: "esp-idf"` until a real schema
exists. See also [`apps/edgitalk-m55-smoke/HOST_PROFILE.md`](../../apps/edgitalk-m55-smoke/HOST_PROFILE.md).

## What lives here

- Integration notes for the RT-Thread / Edgi-Talk host.
- Native glue under [`native/`](native/) (shims, host loop, SCons).
- Portable build notes in [`docs/build.md`](docs/build.md).

## What does not live here

BSP patches and Bluetooth firmware are not vendored in this repository. They stay in:

- [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)
  — Edgi overlay (product bridges, board project, docs) and
  [`projects/Edgi_Talk_M55_PocketJS/docs/`](https://github.com/1024971823/PocketJS_for_Edgi-Talk/tree/main/projects/Edgi_Talk_M55_PocketJS/docs)
  (compile / flash / onboard use).
- The RT-Thread BSP for Edgi-Talk / PSoC E84.

## Upstream

This fork tracks [pocket-nexus/pocketjs](https://github.com/pocket-nexus/pocketjs).
Edgi-Talk host work stays on `host/rt-thread-edgitalk` until it is ready to
propose upstream.
