# Build notes — RT-Thread / Edgi-Talk (PSoC E84 M55)

Portable steps for building the PocketJS package and (when needed) the M55 Rust
static libs. Firmware compile and KitProg3 flash stay in the
[PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)
overlay — this host does not vendor BSP patches, BT firmware, or flash scripts.

## Prerequisites

- [Bun](https://bun.sh/) and the Pocket CLI (`bun tools/pocket.ts` from this repo,
  or `pocket` on `PATH`)
- Optional, only if changing Rust native UI/render crates: Rust toolchain with
  target `thumbv8m.main-none-eabihf` (`rustup target add thumbv8m.main-none-eabihf`)
- RT-Thread BSP for Edgi-Talk / PSoC E84, plus the overlay from
  [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk)
  (BSP patches / BT FW stay there)

## 1. Build the `.pocket` package

From the **pocketjs** repo root (this fork):

```sh
bun tools/pocket.ts build \
  --host-profile apps/edgitalk-m55-smoke/pocket.host.json \
  --manifest apps/edgitalk-m55-smoke/pocket.json
```

Output: `apps/edgitalk-m55-smoke/dist/edgitalk-m55-smoke.pocket`.

The app sources live in [`apps/edgitalk-m55-smoke/`](../../../apps/edgitalk-m55-smoke/).
P0 keeps `pocket.host.json` `platform: "esp-idf"` (borrow of `pocket-idf-host-1`);
a real RT-Thread host id/schema is P1.

The firmware build re-embeds whatever `.pocket` is current — rebuild the package
after UI / chart / asset changes before flashing.

## 2. Rust static libs (optional)

Only needed when changing `hosts/esp-idf/native/` (ui-core / render-rgb565). The
M55 port rebuilds these for `thumbv8m.main-none-eabihf` (HyperRAM ≈ SPIRAM;
ESP shims live in the overlay):

```sh
rustup target add thumbv8m.main-none-eabihf
(cd hosts/esp-idf/native/ui-core && cargo build --release --target thumbv8m.main-none-eabihf)
(cd hosts/esp-idf/native/render-rgb565 && cargo build --release --target thumbv8m.main-none-eabihf)
```

## 3. Firmware + flash

Do **not** copy absolute machine paths here. Follow the overlay docs:

- [编译与烧录.md](https://github.com/1024971823/PocketJS_for_Edgi-Talk/blob/main/projects/Edgi_Talk_M55_PocketJS/docs/%E7%BC%96%E8%AF%91%E4%B8%8E%E7%83%A7%E5%BD%95.md)
  — build firmware (`build-edgi-talk.sh`), KitProg3 flash (`flash-….sh`), serial
- Sibling docs in
  [`projects/Edgi_Talk_M55_PocketJS/docs/`](https://github.com/1024971823/PocketJS_for_Edgi-Talk/tree/main/projects/Edgi_Talk_M55_PocketJS/docs)
  (onboard use, PC companion, desktop preview, perf)

Typical overlay flow (paths relative to that repo / work tree, not this fork):

1. Build `.pocket` as above from pocketjs.
2. Run the overlay firmware build for `Edgi_Talk_M55_PocketJS` (embeds the package).
3. Flash M55 with the KitProg3 script (`M55_PROJECT=Edgi_Talk_M55_PocketJS`).

Native C host glue for this tree is still TBD under [`../native/`](../native/) (P1).
