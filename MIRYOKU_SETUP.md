# Eyelash Sofle — Miryoku (Colemak Mod-DH) + macOS shortcuts

This keyboard runs a faithful [Miryoku](https://github.com/manna-harbour/miryoku)
layout on the 36-key core, with the extra Sofle keys turned into a macOS
shortcut surface. The keymap lives in [`config/eyelash_sofle.keymap`](config/eyelash_sofle.keymap).

A rendered diagram of every layer is regenerated automatically on each push and
committed to [`keymap-drawer/`](keymap-drawer/).

## The 36-key Miryoku core

- **Base alphas:** Colemak Mod-DH, inner 5 columns × 3 rows of each hand.
- **Home-row mods (GACS):** hold `A R S T` = GUI / Alt / Ctrl / Shift (left),
  hold `N E I O` = Shift / Ctrl / Alt / GUI (right). Tap = the letter.
  These use *positional* hold-taps (a mod only triggers when paired with a key
  on the opposite hand or a thumb) plus a 125 ms prior-idle guard, so normal
  typing rolls don't fire modifiers.
- **Thumb layer-taps** (tap = key, hold = layer):

  | Left thumb | | | Right thumb | | |
  |---|---|---|---|---|---|
  | `Esc`/MEDIA | `Space`/NAV | `Tab`/MOUSE | `Enter`/SYM | `Bspc`/NUM | `Del`/FUN |

- **Layers:** `0 Base · 1 Nav · 2 Mouse · 3 Media · 4 Num · 5 Sym · 6 Fun · 7 Button`.
  Nav/Mouse/Media (right-hand clusters) are held with the left thumb;
  Num/Sym/Fun (left-hand clusters) are held with the right thumb. The **Button**
  layer (hold `Z` or `/`) puts one-handed cut/copy/paste/undo/redo on the alphas
  and mouse buttons on the thumbs.

Clipboard keys (Nav/Mouse/Button layers) are macOS **⌘**-based
(⌘Z / ⌘X / ⌘C / ⌘V / ⇧⌘Z).

## The extra keys (macOS shortcut surface)

Everything outside the 36-key core is transparent on the function layers, so
these work no matter which layer you're holding.

| Key (Base) | Sends | macOS action | Works out of the box? |
|---|---|---|---|
| Encoder turn | Vol +/− | Volume | ✅ |
| Encoder press | `C_MUTE` | Mute | ✅ |
| Number row | `1`…`0` | Numbers (matches the caps) | ✅ |
| Center cluster | ↑ ↓ ← → / Enter | Arrows + Enter | ✅ |
| Left outer, top | ⌃↑ | Mission Control | ✅ (default) |
| Left outer, mid | ⌘Space | Spotlight | ✅ (default) |
| Left outer, bottom | ⌃⌘Space | Emoji & Symbols picker | ✅ (default) |
| Right outer, top | **F13** | **Dictation** | ⚠️ needs binding |
| Right outer, mid | **F14** | **OCR / text grab** | ⚠️ needs binding |
| Right outer, bottom | ⌃⌘F | Toggle full screen | ✅ (most apps) |
| Left lower (2) | `C_BRI_DN` / `C_BRI_UP` | Screen brightness | ✅ |
| Right lower (2) | ⌘⇧4 / ⌘⇧5 | Screenshot region / capture tool | ✅ (default) |

Media transport (play/pause, prev, next, stop) lives on the **Media** layer
(hold the left `Esc`/MEDIA thumb) along with volume, RGB controls, and the
USB/BLE output toggle.

### The two keys you must bind host-side

I used dedicated function keys (`F13`, `F14`) rather than guessing your tools'
defaults — bind them once and they never collide with anything:

1. **Dictation → F13**
   System Settings → Keyboard → **Dictation** → turn On. Click the **Shortcut**
   dropdown → **Customize…**, then *press the Dictation key on this keyboard*
   (it records F13). macOS toggles dictation with that key thereafter.

2. **OCR / text grab → F14**
   In your OCR tool (TextSniper, CleanShot X, etc.), set the capture hotkey to
   F14 by *pressing the OCR key on this keyboard* while recording the hotkey.

> **Globe / fn key:** macOS's 🌐 key uses an Apple-proprietary HID usage that
> doesn't transmit reliably over Bluetooth from non-Apple keyboards, so it's not
> mapped. The emoji picker (its most common use) is covered by ⌃⌘Space above.

## Building & flashing

This repo builds in the cloud — no local toolchain needed.

1. **Push** to GitHub. The **Build** workflow (`.github/workflows/build.yml`)
   produces firmware artifacts; the **Draw** workflow refreshes the diagram.
2. From the Actions run, download the artifact and grab the two `.uf2` files:
   `eyelash_sofle_left …` and `eyelash_sofle_right …` (plus `settings_reset` if
   you ever need to wipe pairings).
3. **Flash each half:** double-tap the reset button to mount the `NICENANO`
   USB drive, then drag the matching `.uf2` onto it. Repeat for the other half.
   (`&bootloader` is also on the top-left key of the Nav/Mouse/Media/Num/Sym/Fun
   layers if you prefer a key.)
4. Re-pair over Bluetooth if needed.

ZMK Studio is enabled on the left (central) half, so you can also tweak the
keymap live — but this `.keymap` file remains the source of truth.

## Tuning notes

You picked "full Miryoku, tune later." Likely first adjustments:

- **Home-row mod timing:** `tapping-term-ms` (200), `quick-tap-ms` (175),
  `require-prior-idle-ms` (125) in the `hml`/`hmr` behaviors. Raise the term if
  mods feel hard to trigger; lower the prior-idle if quick mod-chords get eaten.
- **Swap a layer:** the MOUSE layer is the usual candidate to repurpose once you
  settle in — each layer is an independent block in the keymap.
- **Extra-key assignments:** all in the Base layer; the function layers leave
  them transparent, so editing Base changes them everywhere.

Soft-off (BLE deep sleep) is the combo **Q + R + Z** held ~2 s.
