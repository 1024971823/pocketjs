# Build notes (stub)

Placeholder for the RT-Thread / Edgi-Talk (PSoC E84 M55) host. Exact flags follow the Edgi-Talk BSP and [PocketJS_for_Edgi-Talk](https://github.com/1024971823/PocketJS_for_Edgi-Talk). BSP patches and BT firmware are not vendored in this tree.

## 1. Overlay the BSP

Start from the RT-Thread BSP for Edgi-Talk / PSoC E84 and apply the overlay from PocketJS_for_Edgi-Talk. Do not copy BSP patches or Bluetooth firmware into pocketjs.

## 2. Build the app with the Pocket CLI

From a PocketJS checkout, build the smoke app under `apps/edgitalk-m55-smoke` with the Pocket CLI into a package the host can load.

```sh
# illustrative — confirm the package name once the app is synced
pocket build apps/edgitalk-m55-smoke
```

## 3. Flash

Flash the firmware using the flow in the Edgi-Talk docs. This scaffold does not vendor a flash script.
