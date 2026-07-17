# SHOUT

Turn a phone into a giant scrolling sign to get a stranger's attention in a loud
or dark venue. Type a message, hit GO, wave the phone. One self-contained
HTML file — no build, no dependencies, no network, works offline.

**Live:** https://timball.github.io/SHOUT/ — open on a phone and Add to Home Screen.

## Use

1. Open `shout.html` (served over http/https, or added to the home screen).
2. Type a message. The box grows for longer text; a counter turns amber past ~60
   chars as a gentle "this is getting long to scroll" nudge (never blocks).
3. Pick **ATTENTION** and, if not `off`, a **THEME** (or a **COLOR** + **INVERT**
   when `off`).
4. **GO.** The message scrolls in letters as tall as the screen, any orientation.
5. On the sign: **drag up/down** = scroll speed (live), **tap** = back to setup.

Settings (message, colors, mode, theme, speed) persist between launches via
localStorage.

## Modes

**ATTENTION**
- `off` — plain scroll in the chosen COLOR palette (+ INVERT to swap fg/bg).
- `warp` — "hypnotoad": a swirling hue-cycling gradient behind the text.
- `luminance` — the whole field pulses bright↔dark. This is the strongest
  peripheral attention-grab (see rationale below).

**THEME** (drives warp + luminance)
- `rainbow` — full hue wheel.
- `cool` — cyan/blue band.
- `warm` — red/orange/yellow band.
- `green` — tight band near ~555nm.

## Design rationale (why the themes exist)

Human daytime (photopic) vision peaks around **555nm — green**; in the dark, rod
vision takes over and the peak shifts down to **~507nm — cyan/blue** (the Purkinje
effect). So there's no single "most visible" color — it depends on ambient light.
Practical pairing:

- **Bright / outdoors → `green`** (matches the photopic peak).
- **Dark venue → `cool`** (matches the scotopic shift).

But hue matters far less than **luminance contrast** for both legibility and
grabbing attention. That's why the text always carries a heavy black outline, and
why `luminance` mode (full-field bright/dark flicker) is the real "notice me across
the room" grabber — peripheral vision locks onto brightness change, not color.

## Tuning (CONFIG block at the top of the script)

| Knob | Does |
|---|---|
| `defaultSpeed` | Starting scroll speed, px/sec (default 800) |
| `minSpeed` / `maxSpeed` | Drag range bounds |
| `dragDivisor` | Drag sensitivity (smaller = touchier) |
| `fontScale` | Letter height as a fraction of screen height |
| `textOutline` | Black outline halo on the text (keep on for warp/luminance) |
| `softLimit` | Char count where the counter turns amber |
| `warpSpeed` | Swirl pace: 0.4 lava-lamp → 2.5 max hypnosis |
| `warpHueText` | Rainbow only: also hue-shift the text (psychedelic) |
| `warpSaturation` / `warpLightness` | Warp gradient richness/brightness |
| `lumSpeed` | Luminance pulses/sec (0.7 = gentle; kept well below seizure range) |
| `lumSharp` | 1 = smooth sine → higher = sharper on/off |
| `lumMin` / `lumMax` | Dark and bright extremes of the pulse (% lightness) |
| `themes` / `palettes` | The color families and static palettes |

## Deploy

- **GitHub Pages:** served from the repo root at
  https://timball.github.io/SHOUT/ (Settings → Pages → deploy from `main`, root).
  The entry file is `index.html` so the bare URL works. HTTPS is automatic, which
  the Wake Lock and Fullscreen APIs require.
- **Self-hosted (any host):** drop the HTML in a web root behind https. (Also
  served from a home Caddy instance.)
- **iPhone fullscreen:** open in Safari → Share → **Add to Home Screen**, then
  launch from the icon. The `apple-mobile-web-app-capable` meta makes it run
  chromeless. (iOS Safari won't honor the Fullscreen API on non-video elements, so
  home-screen install is the way to true fullscreen there.)
- **Screen stays awake** via the Wake Lock API while a sign is showing (re-acquired
  on tab focus).

## Known limitations / notes

- Outline is a `text-shadow` ring, **not** `-webkit-text-stroke` — the stroke
  traces internal glyph contours and leaves phantom seams inside heavy a/e/k/y.
- No automatic day/night switching: the `AmbientLightSensor` API is effectively
  dead (unshipped on iOS, flag-gated elsewhere) and the camera is off-limits by
  design. Pick the theme manually.
- Single-line scroll: newlines/extra spaces in the message collapse to one line.

## History

v1 (landscape-only, fixed animation) → CONFIG refactor + palettes + persistence →
hypnotoad warp → perception-science themes + luminance mode → segmented-control UI
with disappearing color/theme fork → full-bleed backdrop + outline artifact fix.
