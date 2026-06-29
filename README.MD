# X.Tips X7S / IQX7S Mechanical Keyboard — QMK & Vial Firmware

Community-maintained QMK and Vial firmware for the **X.Tips X7S** (also sold as IQX7S) wireless split mechanical keyboard. The source files were originally provided by the vendor; this repository keeps them up to date with the current [vial-qmk](https://github.com/vial-kb/vial-qmk) toolchain.

## Hardware overview

| Property | Value |
|----------|-------|
| MCU | STM32F103 |
| Bootloader | stm32duino |
| Layout | Split, 36 keys (3×5 + 3 thumb keys per half) |
| Layers | 9 (dynamically programmable via Vial/VIA) |
| RGB | 1 × WS2812 per half (layer-color indicator) |
| Split transport | USART (pin B6) |

## Prerequisites

- [QMK CLI](https://docs.qmk.fm/newbs_getting_started) — install with `pip install qmk`, then run `qmk setup`
- [vial-qmk](https://github.com/vial-kb/vial-qmk) — clone this fork instead of the upstream QMK repo

```bash
git clone https://github.com/vial-kb/vial-qmk.git --recurse-submodules
cd vial-qmk
qmk setup -H .   # point QMK CLI at this clone
```

## Building the firmware

Copy the `x7s` folder into the vial-qmk keyboards directory, then compile:

```bash
cp -r /path/to/x7s vial-qmk/keyboards/x7s

# Vial keymap (recommended — full GUI configurability)
qmk compile -kb x7s -km vial

# VIA keymap (alternative)
qmk compile -kb x7s -km via
```

Output: `x7s_vial.bin` (45 KB) in the vial-qmk root.

> [!NOTE]
> The build produces an LTO warning about serial LTRANS jobs. This is expected and does not affect the firmware.

## Flashing

### Enter bootloader mode

The keyboard uses the **stm32duino** bootloader. To enter it:

1. Unplug the USB cable.
2. Hold the **bootmagic key** (`E` on the left half, or `I` on the right half — 3rd key in the top row).
3. Plug in USB while holding the key, then release.

The board appears as USB device `1EAF:0003` when in bootloader mode.

> [!TIP]
> Alternatively, bind `QK_BOOT` to any key via the Vial or VIA GUI, then tap it to reset without unplugging.

### Flash with QMK CLI

The easiest method — compiles and waits for the device automatically:

```bash
qmk flash -kb x7s -km vial
```

Then trigger bootloader mode (see above). The flash starts as soon as the device is detected.

### Flash manually

```bash
"/Users/<you>/Library/Application Support/qmk/bin/dfu-util" \
  -d 1eaf:0003 -a 2 -D x7s_vial.bin
```

## Using VIA

The keyboard works out of the box with VIA — no firmware changes required.

1. Download the [VIA app](https://www.caniusevia.com/).
2. Load `via-x7.json` from this repository via **File → Import Keymap**.
3. Connect the keyboard and start remapping.

## Using Vial

[Vial](https://get.vial.today/) lets you remap keys, configure tap dance, combos, and more — all without recompiling firmware.

1. Flash the `vial` keymap (see [Flashing](#flashing) above).
2. Open the [Vial app](https://get.vial.today/download/).
3. The keyboard is detected automatically as **X.Tips X7S Keyboard**.
4. To unlock advanced settings (tap dance, combos, key overrides), hold **Q + P** simultaneously for 5 seconds when prompted.

> [!IMPORTANT]
> If you rebuild the firmware from source, generate a new unique `VIAL_KEYBOARD_UID` before flashing:
> ```bash
> python3 -c "import os; print(', '.join(hex(b) for b in os.urandom(8)))"
> ```
> Replace the value in `keymaps/vial/config.h`. Reusing the same UID across multiple physical keyboards causes the Vial app to mix up their saved settings.

## Default keymap

The shipped Vial keymap has 9 layers:

| Layer | Purpose |
|-------|---------|
| 0 | QWERTY alphabet |
| 1 | Numpad & punctuation |
| 3 | Function keys, media, mouse |
| 4 | App hotkeys (MEH modifier) |
| 5 | RGB controls, window management, mouse buttons |
| 6 | Extended F-keys (F13–F24) & numpad |
| 7 | Symbols & arrow keys |

The RGB LED on each half indicates the active layer by color (white = caps lock, cyan = layer 1, coral = layer 2, …). All layers are fully reprogrammable via the Vial app without recompiling.

## Porting notes

The following changes were made to the original vendor source to bring it in line with vial-qmk (protocol `0x00000006`, June 2026):

- Added `keymaps/vial/config.h` — defines `VIAL_KEYBOARD_UID` and the unlock combo (`Q + P`), both required by `quantum/vial.c`.
- Updated `keymaps/vial/vial.json` — added `firmwareVersion`, replaced the deprecated `keycodes`/`menus` fields with `"lighting": {"extends": "qmk_rgblight"}`.
- No changes were needed to `keyboard.json`, `config.h`, `halconf.h`, `mcuconf.h`, `rules.mk`, or `x7s.c`.
