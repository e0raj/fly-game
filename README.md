# Fly

A full-screen, one-file browser arcade game. Tap to lift, glide through the pipes, don't touch the ground.

No build step, no dependencies, no assets — the whole game is a single `index.html` with a canvas and vanilla JavaScript.

## Play

Open `index.html` in any browser, or play it online:

**https://e0raj.github.io/fly-game/**

Locally:

```bash
python3 -m http.server 8000
# then visit http://localhost:8000
```

Made by [@e0raj](https://instagram.com/e0raj) — the Instagram pill sits in the top-left corner of the game.

## Controls

| Action | Input |
| --- | --- |
| Lift | `Space` / `↑` / `W` / click / tap |
| Mute | `M`, or Sound in the menu |
| Pause | `P`, or just open the menu |
| Restart | `R` |
| Change character | swipe left / right, press `←` `→`, tap a portrait, or press `1`–`4` |
| Difficulty | CHILL / NORMAL / INSANE in the menu |
| Play again | tap after the game-over card |

## Characters

Four characters. Swipe left or right anywhere on the start or game-over screen to flick between them — the picker at the bottom mirrors the choice, and tapping a portrait works too. All of them fly on exactly the same physics and share the same hitbox, so the game is never easier or harder depending on who you pick — only the look, the tilt and the trail change. Your choice is remembered between visits.

| Character | What it looks like |
| --- | --- |
| **Bird** | Beating wings, swaying tail, a beak that opens mid-stroke, blinking eye |
| **Rocket** | Capsule body, red fins and nose cone, a flame that flickers and grows on every tap |
| **Paper plane** | Two folded wings that flutter as it glides, with a crease down the middle |
| **Auto-rickshaw** | Yellow-and-black body, spinning wheels, windshield and headlight |

## Difficulty

Three modes, chosen from the menu in the top-right corner and remembered between visits:

| Mode | Gap | Speed |
| --- | --- | --- |
| **CHILL** | 238 | 168 → 250 |
| **NORMAL** | 186 | 205 → 320 |
| **INSANE** | 146 | 242 → 385 |

## Scoring

- One point for clearing a pipe.
- **Two points and a chime for threading it dead centre** — a small reward for flying with control rather than scraping the gap.
- Your best score and a local top five are kept on the device.

## The menu

Everything that is not the game itself lives behind one button. The top-right corner holds a hamburger that opens a small panel with:

- your best score and top-five score
- the difficulty segmented control
- the sound switch
- **How to play**, **About** and **Privacy** pages
- the version number

Opening the menu mid-round pauses the game for you, and tapping anywhere else closes it without flapping.

## Pages

The three pages are real pages — `how-to-play.html`, `about.html` and `privacy.html` — linked from the menu. They share `page.css` and each one links back to the game and across to the others:

- **How to play** — controls, the perfect-gap bonus, characters and difficulty.
- **About** — what the game is, who made it, version, Instagram and source links.
- **Privacy** — plainly states that nothing leaves the device and exactly what is stored locally.

## How it is built

**Full screen, always.** The world uses a virtual coordinate space that is always 720 units tall; the width is derived from the viewport aspect ratio on every resize. So the canvas fills any phone, tablet, laptop or ultrawide monitor with no letterboxing and no stretched art — only more of the world becomes visible. Everything is drawn through one scaled transform at device pixel ratio, so it stays sharp on retina screens.

**Flight.** Deliberately floaty rather than twitchy: a tap lifts you about 72 units and settles back in just under a second, so holding altitude takes a tap roughly every second, and a full-screen drop takes about two seconds — there is always time to react. The nose tips up on a tap and noses over slowly as you sink, so it reads as gliding rather than falling.

**Physics loop.** A fixed 240 Hz timestep with one render per animation frame. Motion is identical on a 60 Hz phone and a 144 Hz monitor, and the scrolling can never stutter or drift.

**The characters.** Every one is drawn procedurally, not from a sprite sheet, and the picker portraits are painted by the same drawing code — what you see in the thumbnail is exactly what flies.

**Day into night.** The sky is driven by your score: it starts as bright day, warms into sunset around twenty points, and settles into night past thirty — the sun sinks and fades, the moon rises, and stars fade in. A soft night wash keeps the green pipes readable against the dark.

**Scenery.** A three-stop sky gradient with a sun halo, drifting volumetric clouds, far birds, two parallax hill ridges, a tree-line silhouette, and a scrolling ground with grass blades and dirt texture. Every layer is drawn from a seamlessly repeating pattern — the ridges are sums of sines whose wavelengths divide the tile exactly, and the trees are keyed to their world index — so nothing ever pops or jumps as it scrolls past. A vignette sits over the whole scene.

**Feel.** A ripple at the point you tap, particle puffs behind the character, sparks and a scale-pop on the score for every point, a feather burst and screen shake on impact, a rolling score counter, medals (Bronze 5, Silver 12, Gold 25, Platinum 40), confetti when you beat your best, and a local top five saved in `localStorage`. Pausing stops the simulation dead rather than freezing the screen mid-frame. On phones a short haptic tick lands on each lift and a longer buzz on a crash, and `prefers-reduced-motion` turns the shake and confetti off for anyone who asks for less movement.

**Sound.** All effects are synthesised live with the Web Audio API, so there are no audio files to download:

- Lift — a bandpass-swept noise whoosh layered with a short falling sine.
- Score — a two-note sine chime that rises in pitch as your streak grows.
- Impact — a low sine thud with a filtered noise crack, followed by a falling whistle.
- New best — a four-note arpeggio.

Mute state is remembered between visits.

## Structure

```
fly-game/
├── index.html          # the game — markup, styles, loop, rendering, audio
├── how-to-play.html    # controls, scoring, characters, difficulty
├── about.html          # what the game is, version, links
├── privacy.html        # what is stored locally, and what is not
├── page.css            # shared stylesheet for the three pages
├── README.md
└── LICENSE
```

Inside the file the code is split into labelled sections: tuning, characters, state, resize, audio, scenery, gameplay, input, the picker, update, drawing helpers, character drawing, HUD, render, and the animation loop.

## Adding your own character

Add an entry to the `SKINS` array near the top of the script, write a `drawX(g, s)` function that draws it around the origin facing right (about 36 units long), and add one line to `drawSkin`. The picker builds itself from the array, and the portrait is painted with the same drawing code, so a new character shows up everywhere at once.

## Tuning it

The feel lives in a handful of constants near the top of the script:

```js
const GRAVITY    = 640;   // px/s² — light, so the character glides
const FLAP       = -305;  // upward impulse
const MAX_FALL   = 330;   // terminal sink speed
const GAP        = 186;   // pipe gap height
const SPACING    = 276;   // distance between pipes
const BASE_SPEED = 205;   // starting scroll speed
const MAX_SPEED  = 320;   // speed cap
```

Raise `GRAVITY` and `FLAP` together for a snappier, more classic-feeling arc; lower them for an even lazier glide. Widen `GAP` or lower `BASE_SPEED` to make it easier.

## License

MIT — do whatever you like with it.
