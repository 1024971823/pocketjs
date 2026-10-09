# Native host glue (TBD — P1)

C host glue for the RT-Thread / Edgi-Talk (PSoC E84 M55) port is **not** in this
tree yet. This directory is a placeholder, not an empty claim that glue is ready.

## Plan

Port from the Edgi overlay at `applications/pocketjs` in
[PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk).

Until then, P0 reuses the ESP-IDF C components under
`hosts/esp-idf/components/{package,guest,ui_*,render_rgb565}` and rebuilds the
Rust crates for `thumbv8m.main-none-eabihf`. The overlay supplies ESP shims and
product drivers (LCD / touch / Wi-Fi / BT).

That overlay, the RT-Thread BSP, and BT firmware stay outside this repository.
See [`../docs/build.md`](../docs/build.md) for portable package / Rust steps.
