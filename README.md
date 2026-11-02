# SW — Birds Flip

A tiny, fast, one-file browser game. Tap to flap, slip through the pipes, don't kiss the ground.

No build step, no dependencies, no assets — the whole game is a single `index.html` with a canvas and vanilla JavaScript.

## Play

Open `index.html` in any browser. That's it.

Or serve it locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Controls

| Action | Input |
| --- | --- |
| Flap | `Space` / `↑` / `W` / click / tap |
| Mute | `M` |
| Restart | `R` |
| Play again | tap after the game-over card appears |

## What's in it

- Fixed 420×700 logical canvas, scaled to fit any screen (phone, tablet, desktop) at device pixel ratio — crisp on retina displays.
- Physics tuned for a short learning curve: gravity, terminal rotation, and a wing that actually flips when you flap.
- Procedural pipe field: gap size fixed, gap position random, scroll speed ramps up with your score.
- Parallax sky with sun glow, drifting clouds, two hill layers and a scrolling ground strip.
- Particle puffs on every flap, sparks on every point, a feather burst on impact.
- Screen shake and a white flash on death.
- Score, best score persisted in `localStorage`, and medals (Bronze 5, Silver 12, Gold 25, Platinum 40).
- Sound effects generated on the fly with the Web Audio API — no audio files, so nothing to download.
- Pauses itself to the ready screen when you switch tabs, so you never lose a run to a background tab.

## Structure

```
sw/
└── index.html    # everything: markup, styles, game loop, rendering, audio
```

Inside the file, the code is split into clear sections: tuning constants, audio, state, input, update, drawing, and the `requestAnimationFrame` loop.

## Tuning it

The feel lives in a handful of constants near the top of the script:

```js
const GRAVITY   = 1500;  // px/s²
const FLAP      = -430;  // upward impulse
const GAP       = 172;   // pipe gap height
const SPACING   = 240;   // distance between pipes
const BASE_SPEED = 168;  // starting scroll speed
const MAX_SPEED  = 300;  // speed cap
```

Want it easier? Widen `GAP` or lower `BASE_SPEED`. Want it brutal? Shrink the gap and raise `MAX_SPEED`.

## License

MIT — do whatever you like with it.
