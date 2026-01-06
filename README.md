# TOTEM Keyboard - Miryoku Layout with Mouse Support

ZMK firmware configuration for the TOTEM 38-key split keyboard with full Miryoku layout and mouse emulation.

## Features

- **Miryoku Layout** - Ergonomic 36-key layout adapted for TOTEM's 38 keys
- **Timeless Home Row Mods** - GACS/SCAG (GUI, Alt, Ctrl, Shift) with anti-misfire configuration
- **Mouse Keys** - Full mouse emulation (movement, scroll, buttons)
- **7 Layers**: Base, Nav, Mouse, Media, Num, Sym, Fun
- **Caps Word** - Smart caps lock (combo: both index fingers)
- **Bluetooth** - 5 device profiles, enhanced TX power

## Layer Overview

```
BASE (QWERTY + Home Row Mods)
├── Hold A/S/D/F = GUI/ALT/CTRL/SHIFT (left hand)
├── Hold J/K/L/' = SHIFT/CTRL/ALT/GUI (right hand)
└── Thumbs: ESC/MEDIA, SPC/NAV, TAB/MOUSE | ENT/SYM, BSPC/NUM, DEL/FUN

NAV   - Arrow keys, Home/End, PgUp/PgDn, Clipboard
MOUSE - Mouse movement, scroll, left/right/middle click
MEDIA - Vol, Play/Pause, Skip, Bluetooth profiles
NUM   - Number pad layout (789/456/123/0)
SYM   - Symbols (!@#$%^&*...)
FUN   - Function keys F1-F12, Print Screen
```

## Building Firmware

### Option 1: GitHub Actions (Recommended)

1. Create a new GitHub repository
2. Push this entire `zmk-config` folder to the repo
3. Go to **Actions** tab → Enable workflows
4. The workflow runs automatically on push
5. Download the `firmware` artifact from the workflow run
6. Extract to get `totem_left-seeeduino_xiao_ble-zmk.uf2` and `totem_right-seeeduino_xiao_ble-zmk.uf2`

### Option 2: Local Build

```bash
# Install west and Zephyr SDK (see ZMK docs)
pip install west

# Initialize west workspace
cd zmk-config
west init -l config
west update

# Build left half
west build -s zmk/app -b seeeduino_xiao_ble -- -DSHIELD=totem_left -DZMK_CONFIG="$(pwd)/config"

# Build right half
west build -s zmk/app -b seeeduino_xiao_ble --pristine -- -DSHIELD=totem_right -DZMK_CONFIG="$(pwd)/config"
```

## Flashing

### Step 1: Get Settings Reset (First Time Only)

Download `settings_reset-seeeduino_xiao_ble-zmk.uf2` from [ZMK releases](https://github.com/zmkfirmware/zmk/releases) or build it yourself.

### Step 2: Flash Each Half

For **each** keyboard half:

1. Connect via USB
2. **Double-tap the reset button** (tiny button labeled RST on XIAO BLE)
3. A USB drive named `XIAO BLE` appears
4. First time: Drag `settings_reset-seeeduino_xiao_ble-zmk.uf2` → wait for reboot
5. Double-tap reset again
6. Drag the appropriate firmware:
   - Left: `totem_left-seeeduino_xiao_ble-zmk.uf2`
   - Right: `totem_right-seeeduino_xiao_ble-zmk.uf2`
7. Board auto-reboots when done

### Step 3: Pair the Halves

1. Turn on both halves (slide switches if present)
2. They auto-pair within seconds
3. The left half appears as "TOTEM Miryoku" on your computer's Bluetooth

## Bluetooth Profiles

Use the **Media layer** (hold left inner thumb) to manage Bluetooth:

| Key       | Action          |
|-----------|-----------------|
| Bottom row| BT profile 0-4  |
| Top right | Clear current profile |

## Key Reference

### Base Layer
```
╭─────────────────────────────────────────╮   ╭─────────────────────────────────────────╮
│   Q     W     E     R     T             │   │             Y     U     I     O     P   │
│   A     S     D     F     G             │   │             H     J     K     L     '   │
│   Z     X     C     V     B    [        │   │        ]    N     M     ,     .     /   │
╰───────────────────╮ ESC  SPC   TAB      │   │      ENT  BSPC  DEL  ╭───────────────────╯
                    ╰─────────────────────╯   ╰─────────────────────╯
```

### Mouse Layer (hold TAB)
```
Right hand:
- HJKL position: Mouse movement (←↓↑→)
- Below HJKL: Scroll (←↓↑→)
- Thumbs: Right Click, Left Click, Middle Click
```

## Customization

### Change to Colemak-DH

Edit `config/totem.keymap` and replace the base layer alpha keys.

### Adjust Mouse Speed

In `config/totem.keymap`, modify:
```c
#define ZMK_POINTING_DEFAULT_MOVE_VAL 1200  // Higher = faster (default 600)
#define ZMK_POINTING_DEFAULT_SCRL_VAL 20    // Scroll speed (default 10)
```

### Adjust Home Row Mod Timing

```c
#define QUICK_TAP_MS 175      // Repeat threshold
#define TAPPING_TERM_MS 280   // Hold threshold
#define PRIOR_IDLE_MS 150     // Idle before mod activation
```

## Troubleshooting

| Issue | Solution |
|-------|----------|
| Halves won't connect | Flash `settings_reset.uf2` on both, then reflash firmware |
| Can't enter bootloader | Hold reset while plugging USB, then release |
| Mouse not working | After flashing, re-pair Bluetooth (HID descriptor changed) |
| Home row mods too sensitive | Increase `PRIOR_IDLE_MS` to 175 or 200 |
| Home row mods too slow | Decrease `TAPPING_TERM_MS` to 250 |

## Credits

- [GEIGEIGEIST](https://github.com/GEIGEIGEIST/TOTEM) - TOTEM keyboard design
- [Miryoku](https://github.com/manna-harbour/miryoku) - Layout design
- [urob](https://github.com/urob/zmk-config) - Timeless home row mods concept
- [ZMK Firmware](https://zmk.dev) - Keyboard firmware
