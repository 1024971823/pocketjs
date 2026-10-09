# Build notes — RT-Thread / Edgi-Talk (PSoC E84 M55)

Portable steps for building the PocketJS package, M55 Rust static libs, and
including the host `SConscript` from a product RT-Thread project. Firmware
compile and KitProg3 flash stay in the
[PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)
overlay — this host does not vendor BSP patches, BT firmware, or flash scripts.

## Prerequisites

- [Bun](https://bun.sh/) and the Pocket CLI (`bun tools/pocket.ts` from this repo,
  or `pocket` on `PATH`)
- Optional, only if changing Rust native UI/render crates: Rust toolchain with
  target `thumbv8m.main-none-eabihf` (`rustup target add thumbv8m.main-none-eabihf`)
- Sibling [quickjs-ng](https://github.com/quickjs-ng/quickjs) checkout (override
  with `POCKETJS_QUICKJS_ROOT`)
- RT-Thread BSP for Edgi-Talk / PSoC E84, plus the overlay from
  [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)

## 1. Build the `.pocket` package

From the **pocketjs** repo root (this fork):

```sh
bun tools/pocket.ts build \
  --host-profile apps/edgitalk-m55-smoke/pocket.host.json \
  --manifest apps/edgitalk-m55-smoke/pocket.json
```

Output: `apps/edgitalk-m55-smoke/dist/edgitalk-m55-smoke.pocket`.

`pocket.host.json` still uses `platform: "esp-idf"` (borrow of `pocket-idf-host-1`).
Tracking for a real RT-Thread host id/schema:
https://github.com/1024971823/pocketjs/issues/2 — see
[`../HOST_PROFILE.md`](../../../apps/edgitalk-m55-smoke/HOST_PROFILE.md).

Rebuild the package after UI / chart / asset changes before flashing.

## 2. Rust static libs (optional)

Only needed when changing `hosts/esp-idf/native/` (ui-core / render-rgb565). The
M55 port rebuilds these for `thumbv8m.main-none-eabihf`:

```sh
rustup target add thumbv8m.main-none-eabihf
(cd hosts/esp-idf/native/ui-core && cargo build --release --target thumbv8m.main-none-eabihf)
(cd hosts/esp-idf/native/render-rgb565 && cargo build --release --target thumbv8m.main-none-eabihf)
```

`native/SConscript` adds those `release/` dirs to `LIBPATH` and links
`pocketjs_idf_ui_core` + `pocketjs_idf_render_rgb565`.

## 3. Include the host SCons fragment from the product project

Point the product build at this monorepo (`POCKETJS_ROOT`), then include:

```python
import os
# POCKETJS_ROOT = path to this pocketjs clone (relative or absolute)
SConscript(os.path.join(POCKETJS_ROOT, 'hosts/rt-thread-edgitalk/native/SConscript'))
```

The fragment (paths relative to the pocketjs repo root):

- Compiles shared `hosts/esp-idf/components/{package,guest,ui_core,ui_qjs,render_rgb565}` C sources
- Compiles `native/{pocketjs_host_loop,pocketjs_rt_compat,quickjs_compat}.c`
- Force-includes QuickJS compat headers from `native/include/`
- Links M55 Rust archives under `hosts/esp-idf/native/.../thumbv8m.main-none-eabihf/release`
- Builds QuickJS-ng from `POCKETJS_QUICKJS_ROOT` (default: sibling `../quickjs-ng`)

**Stay in the overlay SConscript:** `pocketjs_wifi*`, `pocketjs_bt*`, game /
synth / music / dashboard, BT firmware `btfw.c`, and product-generated package
blobs. See [`../native/README.md`](../native/README.md).

Env overrides: `POCKETJS_QUICKJS_ROOT`, `POCKETJS_SMOKE_PACKAGE`,
`POCKETJS_DEBUG_TOUCH=1`.

## 4. Firmware + flash

Do **not** copy absolute machine paths here. Follow the overlay docs:

- [编译与烧录.md](https://github.com/1024971823/PocketJS_for_Edgi-Talk/blob/main/projects/Edgi_Talk_M55_PocketJS/docs/%E7%BC%96%E8%AF%91%E4%B8%8E%E7%83%A7%E5%BD%95.md)
- Sibling docs in
  [`projects/Edgi_Talk_M55_PocketJS/docs/`](https://github.com/1024971823/PocketJS_for_Edgi-Talk/tree/main/projects/Edgi_Talk_M55_PocketJS/docs)

Typical flow:

1. Build `.pocket` as above from pocketjs.
2. Run the overlay firmware build for `Edgi_Talk_M55_PocketJS` (embeds the package).
3. Flash M55 with the KitProg3 script (`M55_PROJECT=Edgi_Talk_M55_PocketJS`).
