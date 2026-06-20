# Sofle (58-key) — Miryoku (Colemak Mod-DH) + macOS shortcuts

A faithful [Miryoku](https://github.com/manna-harbour/miryoku) layout on the
36-key core, with the remaining Sofle keys as a macOS, non-typing shortcut
surface. Keymap: [`config/eyelash_sofle.keymap`](config/eyelash_sofle.keymap).
A diagram of every layer is in [`keymap-drawer/`](keymap-drawer/) (CI regenerates
it on push).

## The board

Standard **58-key Sofle**: 6 cols × 4 rows per hand (48) + **5 thumb keys per
hand** (10). **No encoder, no center arrow pad, no mute key** — those PCB
positions are unpopulated, so they're `&none` in the firmware.

## The 36-key Miryoku core (all typing)

- **Colemak Mod-DH** alphas, inner 5 × 3 of each hand.
- **Home-row mods (GACS):** hold `A R S T` = GUI/Alt/Ctrl/Shift (left),
  `N E I O` = Shift/Ctrl/Alt/GUI (right). Positional hold-taps + a prior-idle
  guard keep typing rolls from firing mods.
- **Thumb layer-taps** on the inner 3 of each 5-key cluster (tap = key, hold =
  layer): `Esc`/Media `Space`/Nav `Tab`/Mouse · `Enter`/Sym `Bspc`/Num `Del`/Fun.
- **Layers:** `Base · Nav · Mouse · Media · Num · Sym · Fun · Button · Shortcuts`.
  The Media layer carries Bluetooth (BT 0–3, Shift = select+clear), output
  toggle, external power, RGB, volume, and media transport.

Clipboard (Nav/Mouse/Button) is macOS ⌘-based.

## The extra keys — two tiers

The design: **bare keys = instant, no-layer actions for when you're *not*
typing** (one hand on the mouse, or dictating). A deeper palette is one thumb
hold away.

### Tier 1 — bare keys (act on macOS)

| Where | Keys |
|---|---|
| Top row, left | Mission Control · App Exposé · Space ← · Space → · Spotlight · Look-up |
| Top row, right | Prev · Play/Pause · Next · Screenshot region · Screenshot tool · Fullscreen |
| Left outer column | Back (⌘[) · Volume Up · Volume Down |
| Right outer column | Forward (⌘]) · Brightness Up · Brightness Down |
| Outer thumb keys | `53` Mute · `54` **Dictation** · `62` **OCR** · `63` hold → Shortcuts |

### Tier 2 — Shortcuts layer (hold the outer-right thumb, `63`)

| Where | Keys |
|---|---|
| Left hand | **Window tiling:** Left / Right / Top / Bottom / Fill / Center |
| Right hand | Force Quit (⌘⌥Esc) · Reopen tab (⌘⇧T) · App switcher (⌘Tab) · Lock · Emoji · Zoom − / + |

## Host-side setup

Most keys are macOS defaults and work immediately. Three things need a one-time
binding:

1. **Dictation → F13.** System Settings → Keyboard → Dictation → on; Shortcut →
   *Customize* → press the Dictation key (records F13).
2. **OCR → F14.** In your OCR app (TextSniper, CleanShot, …), set the capture
   hotkey to F14 by pressing the OCR key.
3. **Window tiling → reassign the menu items.** macOS's native tiling defaults
   use the **Globe key**, which a non-Apple Bluetooth keyboard can't send. So the
   Shortcuts-layer window keys send **Control+Option+Command + arrows / F / C**,
   and you point the native commands at those:

   System Settings → Keyboard → Keyboard Shortcuts → **App Shortcuts** → `+`,
   Application = **All Applications**, then add each (Menu Title → shortcut):

   | Menu Title | Shortcut to record |
   |---|---|
   | `Left` | ⌃⌥⌘← |
   | `Right` | ⌃⌥⌘→ |
   | `Top` | ⌃⌥⌘↑ |
   | `Bottom` | ⌃⌥⌘↓ |
   | `Fill` | ⌃⌥⌘F |
   | `Center` | ⌃⌥⌘C |

   (These combos avoid the VoiceOver ⌃⌥ and Fullscreen ⌃⌘F conflicts.)

> The Globe key itself isn't mapped: it's an Apple vendor-HID usage macOS only
> honors from a real Apple keyboard's VID/PID. Emoji (⌃⌘Space), Dictation (its
> own shortcut), Mission Control / Spaces (⌃-based) all have non-Globe defaults
> we use instead — window tiling was the only thing that needed it.

## Building & flashing

Cloud build — no local toolchain.

1. **Push** to GitHub → the **Build ZMK firmware** Actions run produces the
   artifacts. *(First run on a fresh fork must be enabled once in the Actions tab.)*
2. Download the `firmware` zip → `eyelash_sofle_left …`, `eyelash_sofle_right …`,
   `settings_reset …` `.uf2`.
3. **Flash each half:** double-tap reset → drag the matching `.uf2` onto the
   `NICENANO` drive. If the halves don't sync after a big change, flash
   `settings_reset` to both first, then re-flash.
4. Re-pair over Bluetooth: hold the left `Esc`/Media thumb → tap BT 0–3.

## Deviations from canonical Miryoku

The 36-key layer content is ported faithfully from
[miryoku_zmk](https://github.com/manna-harbour/miryoku_zmk). Tracked, intentional
differences (tagged in the keymap):

| Tag | Change | Why |
|---|---|---|
| **D1** | Home-row mods / Z·/ / thumb taps use positional hold-taps (`balanced` + trigger positions + prior-idle) instead of canonical `tap-preferred`. | Same tap/hold meaning, far fewer misfires. Set to `tap-preferred` for stock feel. |
| **D2** | Layer-management meta keys → `&none` (bootloader kept). | Single fixed Colemak-DH base. |
| **D3** | Mac (⌘-based) clipboard. | macOS keyboard. |
| **E1** | 22 extra Sofle keys carry the macOS shortcut surface above. | Additive — Miryoku is silent on extra keys. |

See [TUNING.md](TUNING.md) for home-row-mod timing and other adjustments.
