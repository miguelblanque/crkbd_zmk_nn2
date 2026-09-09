# CRKBD USB rescue build

This temporary branch is based on `master` through commit `e362a29` and pins
official ZMK `v0.3.0`. It exists only to recover and isolate the left central
half of a Corne V3 with a nice!nano v2.

The normal rescue image keeps matrix scanning, USB HID, settings and the split
radio, while disabling:

- OLED/display initialization;
- RGB/WS2812;
- external VCC control;
- deep sleep;
- the custom status screen and all locale changes.

The USB product name is `CRKBD-RESCUE`, so a successful boot is unambiguous in
`hidutil list` and IORegistry. Output can be forced from the left half alone by
holding Lower and pressing the physical `F` key.

## Build

Push branch `test/usb-rescue-left` and run **Build ZMK firmware**. The artifact
contains:

- `crkbd-settings-reset-nice-nano-v2.uf2`;
- `crkbd-rescue-left-nice-nano-v2.uf2`;
- `crkbd-rescue-left-debug-nice-nano-v2.uf2`.

Flash settings reset first and then the normal rescue image. Keep the right
half and both battery switches off. Use the debug image only if the normal
image still fails to enumerate.
