# Tuning the Miryoku layout

The defaults in [`config/eyelash_sofle.keymap`](config/eyelash_sofle.keymap)
are sane starting points. After living with the layout for a few days you'll
likely want to adjust a couple of things. Change one knob at a time, in small
steps, and re-flash. (ZMK Studio on the left half can also preview some edits
live, but this `.keymap` file stays the source of truth.)

How to iterate: edit the keymap → commit/push → download the new `.uf2` from the
GitHub Actions **Build** run → double-tap reset on each half → drag the file on.

---

## 1. Home-row-mod timing (most important)

Your `A R S T` / `N E I O` keys are dual-function — **tap** = letter, **hold** =
modifier (GUI / Alt / Ctrl / Shift). The firmware decides which you meant from
timing. Three knobs control it, in the `hml` (left) and `hmr` (right) behaviors:

```
tapping-term-ms = <200>;        // hold must last this long to count as a modifier
quick-tap-ms = <175>;           // tap-then-press within this window repeats the letter, never a mod
require-prior-idle-ms = <125>;  // if another key was pressed <125ms ago, force a tap (no mod)
```

Two structural guards are already on and usually do the heavy lifting:

- **Positional hold-tap** — a home-row mod only becomes a modifier if the *next*
  key is on the **opposite hand or a thumb**. Same-hand pairs (e.g. `S`+`T`) can
  never misfire a modifier. (Defined by the `KEYS_L` / `KEYS_R` / `KEYS_T`
  position lists at the top of the keymap.)
- **`hold-trigger-on-release`** — finalizes the tap/hold decision based on
  release order, which is more forgiving during fast rolls.

### Symptom → fix

| What you feel | Likely cause | Adjustment |
|---|---|---|
| Letters randomly turn into modifiers mid-word (stray Ctrl-click, app shortcut fires) | term and/or idle guard too short | raise `require-prior-idle-ms` toward **150**, then `tapping-term-ms` toward **230** |
| Mods feel sluggish — holding for Shift is slow to register | term too long | lower `tapping-term-ms` toward **170** |
| You hold for a mod right after typing and get the letter instead | idle guard too aggressive | lower `require-prior-idle-ms` toward **100** |
| Fast double letters (the `ll` in "ball") feel off | quick-tap window | raise `quick-tap-ms` slightly (toward **200**) |

Move ~15–25 ms per step. Keep `hml` and `hmr` in sync unless one hand misbehaves
more than the other. If you mostly want mods to "just work" and don't mind the
occasional held-letter, bias toward a **higher** term + idle guard; if you type
fast and value crisp letters, bias **lower** and lean on the positional guard.

---

## 2. Thumb layer-tap timing (secondary)

The six thumb keys are also tap/hold (tap = `Esc`/`Space`/`Tab`/`Enter`/`Bspc`/
`Del`, hold = layer). They use the `tlt` behavior, with **no** positional guard
(thumbs are deliberate and routinely combine with same-hand keys):

```
tapping-term-ms = <200>;
quick-tap-ms = <175>;
```

- Layers feel **slow** to engage → lower `tapping-term-ms` (toward **170**), or
  switch `flavor` from `"balanced"` to `"hold-preferred"` so a held thumb commits
  to the layer sooner (at the cost of occasional accidental layer taps).
- You get **accidental layer activations** when you meant to tap Space/Enter →
  raise `tapping-term-ms`, keep `flavor = "balanced"`.

---

## 3. The Mouse layer — keep it (no pointer is populated)

This board's pointer/scroll device is **not populated**, so there's no trackball
or trackpad. The Miryoku **Mouse** layer still works fully — `&mmv` (move),
`&msc` (scroll), and `&mkp` (buttons) emit HID mouse reports directly from the
keyboard, independent of any physical sensor. That makes it your **only**
on-keyboard pointing, so it's worth keeping rather than repurposing.

If it feels too slow or too fast, tune the pointer speed/acceleration (these
already live near the top of the keymap):

```
#define ZMK_POINTING_DEFAULT_MOVE_VAL 1200   // cursor speed (stock 600)
#define ZMK_POINTING_DEFAULT_SCRL_VAL 25     // scroll speed (stock 10)

&mmv { time-to-max-speed-ms = <500>; acceleration-exponent = <1>; ... };  // ramp-up
&msc { time-to-max-speed-ms = <100>; ... };
```

Raise `MOVE_VAL` for a faster cursor; lower `time-to-max-speed-ms` for a snappier
ramp. The `zip_xy_scaler` / `zip_scroll_scaler` input processors only affect a
*physical* pointer, so they're inert here and can be ignored.

> If you do use an external mouse/trackpad full-time and want the layer back for
> something else (window snapping, app launchers, a macro pad), it's the
> self-contained `layer_2 { … }` block — rewrite its 64 bindings freely; just
> keep the left `Tab`/Mouse thumb as its activator and update `display-name`.

---

## 4. Other knobs worth knowing

- **Encoder** — currently volume (cw = up, ccw = down) on every layer, via the
  `rsr_vol` behavior. To make it do something else on a specific layer, change
  that layer's `sensor-bindings`. A `scroll_encoder` behavior is already defined
  if you'd prefer scroll.
- **Soft-off combo** — `Q + R + Z` held ~2 s (deep sleep). Move it by editing
  `key-positions` in the `softoff` combo, or remove the combo block entirely.
- **RGB underglow** — controls are on the **Media** layer; defaults
  (off-on-start, brightness cap 60, hue) live in `config/eyelash_sofle.conf`.
- **Adding a tap-for-symbol on a thumb, mod-morphs, combos for `( ) { }`, etc.**
  — all standard ZMK; add them in the `behaviors`/`combos` blocks. Ask if you
  want any of these wired up.

---

## Quick reference: what's where in the keymap

| Thing | Location in `config/eyelash_sofle.keymap` |
|---|---|
| Layer numbers | `#define BASE 0` … `#define BUTTON 7` |
| Positional HRM key groups | `#define KEYS_L / KEYS_R / KEYS_T` |
| Home-row-mod timing | `hml` / `hmr` behaviors |
| Thumb layer-tap timing | `tlt` behavior |
| Outer-bottom layer-taps (`Z`/`/`) | `ltl` / `ltr` behaviors |
| Pointer speed/accel | `ZMK_POINTING_*` defines + `&mmv` / `&msc` |
| Each layer's keys | `layer0` … `layer_7` nodes (see `display-name`) |
