# Bird Flip

A full-screen, one-file browser game. Tap to flap, thread the pipes, don't touch the ground.

No build step, no dependencies, no assets — the entire game is a single `index.html` with a canvas and vanilla JavaScript.

## Play

Open `index.html` in any browser, or play it online:

**https://e0raj.github.io/bird-flip-game/**

Locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

## Controls

| Action | Input |
| --- | --- |
| Flap | `Space` / `↑` / `W` / click / tap |
| Mute | `M` or the speaker button |
| Fullscreen | `F` or the corner button |
| Restart | `R` |
| Play again | tap after the game-over card |

## How it is built

**Full screen, always.** The world uses a virtual coordinate space that is always 720 units tall; the width is derived from the viewport aspect ratio on every resize. So the canvas fills any phone, tablet, laptop or ultrawide monitor with no letterboxing and no stretched art — only more of the world becomes visible. Everything is drawn through one scaled transform at device pixel ratio, so it stays sharp on retina screens.

**The bird.** The bird is drawn procedurally, not from a sprite sheet:

- A two-stage wing stroke — a fast downstroke on every tap that settles into a gentle idle flutter — driven by a damped interpolation, so it never snaps.
- Separate back wing, front wing with layered feathers, tail that sways against vertical velocity, and a beak that opens slightly mid-stroke.
- Rotation eases toward a target derived from velocity, giving a smooth dive-and-climb arc instead of a linear tilt.
- Squash and stretch on flap, plus velocity-based stretch while falling.
- A pupil that tracks vertical motion and a random blink every few seconds.
- A soft ground shadow that shrinks as the bird climbs.

**Sound.** All effects are synthesised live with the Web Audio API, so there are no audio files to download:

- Flap — a bandpass-swept noise whoosh layered with a short falling sine.
- Score — a two-note sine chime that rises in pitch as your streak grows.
- Impact — a low sine thud with a filtered noise crack, followed by a falling whistle.
- New best — a four-note arpeggio.

Mute state is remembered between visits.

**Scenery.** A three-stop sky gradient with a sun halo, drifting volumetric clouds, far birds, two parallax hill ridges, a tree-line silhouette, and a scrolling ground with grass blades and dirt texture. A vignette sits over the whole scene.

**Game feel.** Particle puffs on each flap, sparks on each point, a feather burst on impact, screen shake and a white flash on death, a score counter that rolls up instead of jumping, medals (Bronze 5, Silver 12, Gold 25, Platinum 40), and a best score saved in `localStorage`. The game pauses itself to the title screen when you switch tabs so you never lose a run to a background tab.

## Structure

```
bird-flip-game/
└── index.html    # everything: markup, styles, game loop, rendering, audio
```

Inside the file the code is split into clearly labelled sections: tuning, state, resize, audio, scenery, gameplay, input, update, drawing helpers, the bird, HUD, render, and the animation loop.

## Tuning it

The feel lives in a handful of constants near the top of the script:

```js
const GRAVITY    = 2100;  // px/s²
const FLAP       = -615;  // upward impulse
const GAP        = 196;   // pipe gap height
const SPACING    = 318;   // distance between pipes
const BASE_SPEED = 235;   // starting scroll speed
const MAX_SPEED  = 375;   // speed cap
```

Easier: widen `GAP` or lower `BASE_SPEED`. Harder: shrink the gap and raise `MAX_SPEED`.

## License

MIT — do whatever you like with it.
