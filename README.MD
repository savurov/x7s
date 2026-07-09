# X.Tips X7S / IQX7S Mechanical Keyboard - QMK & Vial Firmware

Follow these steps to build and flash the Vial firmware.

1. Install QMK CLI by following the first three steps in the [installation guide](https://docs.qmk.fm/newbs_getting_started).

2. Create a workspace. You can name it whatever you want.

```bash
mkdir keyboard
cd keyboard
```

3. Clone `vial-qmk` (about 2 GB of data).

```bash
git clone https://github.com/vial-kb/vial-qmk.git --recurse-submodules
```

4. Clone this repository as well.

```bash
git clone https://github.com/savurov/x7s.git vial-qmk/keyboards/x7s
```

5. Set up the downloaded data in the QMK CLI.

```bash
cd vial-qmk
qmk setup -H .
```

6. Compile.

```bash
qmk compile -kb x7s -km vial
```

7. Flash your keyboard.

```bash
qmk flash -kb x7s -km vial
```

Enter bootloader mode when the flash command is waiting for the keyboard:

1. Unplug the USB cable.
2. Hold the **bootmagic key** (`E` on the left half, or `I` on the right half - 3rd key in the top row).
3. Plug in USB while holding the key, then release it.

8. Visit https://vial.rocks/ and enjoy.

### Keywords

- x7s keyboard vial
- IQX7S mechanical keyboard
- X.Tips X7 mechanical keyboard
- QMK firmware for IQX7S
- Vial firmware support for IQX7S
- VIA setup for IQX7S keyboard
- Custom mechanical keyboard IQX7S
- Bootloader mode for IQX7S keyboard
- X.Tips X7 keyboard QMK
