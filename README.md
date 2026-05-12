# Crystal Palace — Web Config App

Single-page web app for configuring Crystal Palace crystals over BLE. No build, no install — open `index.html` (or visit the live URL) in a supported browser and it just works.

**Live URL:** https://jesstr8803.github.io/crystal-token/

## What it does

Connects to a crystal during its Phase-1 BLE config window and lets you set:

- Base color (picker + hex input, with live preview as you drag)
- Device name (up to 8 chars)
- Brightness cap (5–100)
- Device ID (1–255)
- Group ID (1–255 — crystals only mesh with the same group)

Also handles:

- **OTA firmware updates** — pick a signed `crystal_palace_dfu_*.zip` and flash it to the crystal over BLE
- **Demo mode** — preview color/brightness on a software crystal in-page, no hardware needed
- **Hardware ID readout** — the chip's factory-burned 32-bit ID, useful for distinguishing units

## Browser support

This app uses **Web Bluetooth**, which is only available in Chromium-based browsers:

| Platform | Browser | Works? |
|---|---|---|
| Android | Chrome, Edge, Brave | ✅ |
| Windows | Chrome, Edge, Brave | ✅ |
| macOS | Chrome, Edge, Brave | ✅ |
| Linux | Chrome (with experimental Web Bluetooth flag) | ⚠️ flaky |
| iOS / iPadOS | Safari | ❌ — Apple doesn't support Web Bluetooth in Safari |
| iOS / iPadOS | Bluefy app | ✅ — third-party WB-enabled browser |

If you're on iPhone/iPad and don't want to install Bluefy, use a desktop or Android device for configuration.

## How to configure a crystal

1. **Enter config mode:** Pop the crystal's battery out and reinsert it (or for the dev jigs, unplug/replug USB). The crystal enters Phase 1 for 30 seconds — it advertises as `CP-XXXX` (where XXXX is the bottom 16 bits of its hardware ID).
2. **Open the web app** in Chrome (or another Chromium browser).
3. **Click "Connect"** — a system dialog shows nearby crystals. Pick yours.
4. **Edit settings.** Color picker updates the crystal live as you drag (~12 Hz, throttled to avoid flooding GATT). Other fields stage locally until you save.
5. **Click "Save to Crystal"** — all current values flush to the crystal's flash and persist across reboots / battery swaps.
6. **Done** — the crystal will time out of Phase 1, save your changes, and enter mesh mode.

## How to update firmware (OTA)

1. Get the `.zip` package — usually `crystal_palace_dfu_v<N>.zip` from the firmware build.
2. Power-cycle the crystal to enter Phase 1.
3. Click "Update Firmware" → pick the zip → "Connect & Flash."
4. The crystal reboots into its bootloader, advertises as `DfuTarg`, and the app uploads the firmware in ~20 KB / 5-chunk blocks. Typical OTA takes about 30–45 seconds.
5. The crystal reboots into the new firmware automatically.

If the OTA stalls or fails, just power-cycle and retry — the bootloader is robust to retries and will pick up where it left off (or restart cleanly).

## Files

- `index.html` — the entire app: HTML, CSS, JS, color picker logic, BLE config
- `dfu.js` — Nordic Secure DFU client (Web Bluetooth implementation of the same protocol `firmware/tools/dfu_ble_client.py` implements in Python)
- `qr-card.html` — printable QR-code cards for gift-bag inserts (10 per letter page, point to the live URL)

## Hosting

Served as a static page from GitHub Pages on the `main` branch of this repo. To update production:

```
git add -p
git commit -m "describe change"
git push
```

GitHub Pages rebuilds within ~30 s.
