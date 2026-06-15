# Technical Specification
## SpaceX Mission to Mars -- Browser Runner Game

Document Version: 1.0
Status: Draft
Author: Mark Ward (MainRobin Pty Ltd)
Date: June 2026

---

## 1. Overview

This document specifies the technical architecture, game systems, rendering
approach, and implementation constraints for SpaceX: Mission to Mars. The
entire game is implemented in a single self-contained `index.html` file using
vanilla JavaScript and the HTML5 Canvas API. No build tools, no frameworks,
no external dependencies.

---

## 2. Technology Stack

| Layer | Technology | Rationale |
|---|---|---|
| Runtime | HTML5 + Vanilla JavaScript (ES2020+) | Zero dependencies, universal browser support |
| Rendering | HTML5 Canvas 2D API | Hardware-accelerated 2D, sufficient for a 2D runner |
| Styling | Inline CSS within index.html | Single-file constraint |
| Audio | Web Audio API (optional, graceful degradation) | No external audio files required |
| Deployment | GitHub Pages (static) | Zero backend, free, instant |
| Assets | Canvas-drawn primitives + inline SVG paths | No external image files |

---

## 3. File Structure

The entire game is a single file:

```
spacex-mission-to-mars/
    index.html          (the complete game)
    README.md
    docs/
        ConOps.md
        Spec.md
        requirements.md
```

`index.html` internal structure:

```html
<!DOCTYPE html>
<html>
<head>
  <!-- Meta, title, viewport -->
  <style>/* All CSS /</style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <script>
    // -- Constants ------------------------------
    // -- Asset definitions (drawn via Canvas) ---
    // -- Game state -----------------------------
    // -- Input handling -------------------------
    // -- Entity classes -------------------------
    // -- Spawning system ------------------------
    // -- Collision detection --------------------
    // -- Game loop ------------------------------
    // -- Screen renderers -----------------------
    // -- Initialisation -------------------------
  </script>
</body>
</html>
```

---

## 4. Canvas and Layout

```javascript
const CANVAS_WIDTH  = 900;   // logical width (px)
const CANVAS_HEIGHT = 500;   // logical height (px)
```

The canvas scales to fill the browser window while maintaining aspect ratio,
using CSS `object-fit: contain` or a JavaScript resize handler. On mobile,
the canvas rotates to landscape orientation with a prompt if the device is
in portrait.

The canvas is always centred horizontally and vertically on the page.
Background behind the canvas: `#000000`.

---

## 5. Game State Machine

State transitions:
- TITLE to COUNTDOWN (spacebar or click)
- COUNTDOWN to PLAYING (after 3-second countdown)
- PLAYING to PAUSED (P key)
- PAUSED to PLAYING (P key)
- PLAYING to GAME_OVER (lives reach zero)
- GAME_OVER to TITLE (R key or Play Again)

```javascript
const GameState = {
  TITLE:     'TITLE',
  COUNTDOWN: 'COUNTDOWN',
  PLAYING:   'PLAYING',
  PAUSED:    'PAUSED',
  GAME_OVER: 'GAME_OVER',
};
```

State transitions:
- `TITLE -> COUNTDOWN`: spacebar / click / touch
- `COUNTDOWN -> PLAYING`: after 3-second countdown completes
- `PLAYING -> PAUSED`: P key
- `PAUSED -> PLAYING`: P key
- `PLAYING -> GAME_OVER`: lives reach zero
- `GAME_OVER -> TITLE`: R key or Play Again button

---

## 6. Game Loop

```javascript
let lastTime = 0;

function gameLoop(timestamp) {
  const deltaTime = Math.min((timestamp - lastTime) / 1000, 0.05); // seconds, capped at 50ms
  lastTime = timestamp;

  update(deltaTime);
  render();

  requestAnimationFrame(gameLoop);
}

requestAnimationFrame(gameLoop);
```

`deltaTime` is capped at 50ms (equivalent to 20fps minimum) to prevent large
position jumps if the tab loses focus or the browser throttles the loop.

All position and velocity values are specified in *pixels per second* and
multiplied by `deltaTime` each frame.

---

## 7. Player (Starship)

```javascript
const PLAYER = {
  x: 150,                    // fixed horizontal position
  y: CANVAS_HEIGHT / 2,      // vertical -- controlled by player
  width: 60,
  height: 30,
  velocityY: 0,
  GRAVITY: 600,              // px/s^2 downward acceleration
  THRUST: -800,              // px/s^2 upward acceleration (while key held)
  MAX_VELOCITY_Y: 500,       // terminal velocity cap (px/s)
  lives: 3,
  invincible: false,
  invincibleTimer: 0,
  INVINCIBLE_DURATION: 2.0,  // seconds after collision
};
```

Hitbox: The player's collision hitbox is 80% of the visual sprite size,
centred on the sprite -- this gives a slightly forgiving feel and matches
player perception. Hitbox dimensions: `width  0.8` x `height * 0.8`.

Vertical bounds: Player cannot move above `y = 20` or below
`y = CANVAS_HEIGHT - 20`. Hitting a boundary clamps position and zeroes velocity.

*Visual: Starship drawn with Canvas 2D primitives -- a sleek white/silver
elongated body with engine glow effect when thrusters are active. No external
image files.

---

## 8. Background Layers (Parallax)

Three scrolling background layers at different speeds create depth:

| Layer | Content | Speed multiplier |
|---|---|---|
| 0 (slowest) | Distant stars (static dots) | 0.1x game speed |
| 1 | Mid-field stars + nebula wash | 0.3x game speed |
| 2 (fastest) | Near stars (larger, brighter) | 0.6x game speed |

Each layer tiles seamlessly -- when a layer scrolls fully off the left edge,
it wraps to the right.

Mars approach effect (REQ-007): When score exceeds `MARS_APPROACH_SCORE`,
a reddish gradient progressively overlays the background. At `MARS_ARRIVAL_SCORE`
the background is a full Mars surface horizon scene.

---

## 9. Entity System

All moving game entities (obstacles and collectibles) share a base structure:

```javascript
class Entity {
  constructor(x, y, width, height, speedX, type) {
    this.x = x;
    this.y = y;
    this.width = width;
    this.height = height;
    this.speedX = speedX;   // px/s (negative = moving left)
    this.type = type;
    this.active = true;
  }

  update(deltaTime, gameSpeed) {
    this.x += this.speedX  gameSpeed * deltaTime;
    if (this.x + this.width < 0) this.active = false; // off-screen left
  }
}
```

Inactive entities are removed from the active arrays each frame.

---

## 10. Obstacle Definitions

All obstacles extend `Entity`. Visual rendering uses Canvas 2D primitives only.

| Type | Width | Height | Base SpeedX | Visual |
|---|---|---|---|---|
| `DEBRIS` | 30--60 (random) | 20--50 (random) | -300 | Jagged grey polygon |
| `SATELLITE` | 70 | 40 | -180 | Silver box with solar panels |
| `ASTEROID` | 40--80 (random) | 40--80 (random) | -250 | Brown irregular circle |
| `ROADSTER` | 55 | 25 | -450 | Red sports car silhouette (rare) |
| `PAPERWORK` | 20 | CANVAS_HEIGHT | -150 | Grey wall with a gap |

Paperwork wall gap: The gap height is `180px`, positioned randomly between
`y = 60` and `y = CANVAS_HEIGHT - 240`. Gap narrows as difficulty increases
(minimum `130px`).

*Roadster rarity: Maximum 1 Roadster active at a time. Spawn probability
is 5% of obstacle spawn rolls. Plays a brief synthesised "Starman" chord via
Web Audio API when it appears.

---

## 11. Collectible Definitions

| Type | Width | Height | Base SpeedX | Score | Visual |
|---|---|---|---|---|---|
| `FUEL` | 20 | 35 | -280 | +100 | Yellow cylinder |
| `STARLINK` | 30 | 15 | -280 | +200 | Flat white rectangle with antenna |
| `DRAGON` | 35 | 35 | -260 | +1 life | White capsule with windows |
| `XPOST` | 25 | 25 | -300 | Speed reduction 5s | Black X logo |

---

## 12. Procedural Spawning System

```javascript
const SPAWN_INTERVAL_BASE  = 2.2;  // seconds between spawn rolls
const SPAWN_INTERVAL_MIN   = 0.8;  // floor as difficulty increases
const OBSTACLE_PROBABILITY = 0.70; // 70% chance of obstacle vs collectible
```

Spawn logic each interval:
1. Roll random number 0--1
2. If < `OBSTACLE_PROBABILITY`: spawn a random obstacle pattern
3. Else: spawn a random collectible
4. Spawn position: `x = CANVAS_WIDTH + 20`, `y = random within safe vertical range`

*Obstacle patterns* (pre-defined arrays of relative positions):
- `SOLO` -- single obstacle, random height
- `HIGH_LOW` -- two obstacles, one high one low, player chooses gap
- `WAVE` -- three obstacles in a vertical wave pattern
- `CORRIDOR` -- two parallel obstacles, player threads the gap
- `SWARM` -- four small debris items scattered across height

Pattern selection is random with equal weighting. Patterns with multiple
entities create all entities at spawn time.

---

## 13. Collision Detection

AABB (axis-aligned bounding box) collision per frame:

```javascript
function checkCollision(player, entity) {
  const ph = player.hitbox; // {x, y, width, height} -- 80% of sprite
  return (
    ph.x < entity.x + entity.width  &&
    ph.x + ph.width > entity.x      &&
    ph.y < entity.y + entity.height &&
    ph.y + ph.height > entity.y
  );
}
```

On collision with an obstacle (and player not invincible):
1. Decrement `PLAYER.lives`
2. Set `PLAYER.invincible = true` for `INVINCIBLE_DURATION` seconds
3. Play collision sound (Web Audio API)
4. Screen flash red for 200ms
5. If lives === 0: transition to `GAME_OVER`

On collision with a collectible:
1. Apply collectible effect (score bonus, life gain, speed reduction)
2. Mark collectible inactive
3. Play pickup sound
4. Show floating score text at collection point (+100, +200, etc.)

---

## 14. Difficulty Progression

```javascript
const SPEED_INITIAL    = 1.0;   // global speed multiplier at game start
const SPEED_MAX        = 3.2;   // maximum speed multiplier
const SPEED_RAMP_RATE  = 0.04;  // multiplier increase per second
```

Each frame:
```javascript
gameSpeed = Math.min(SPEED_INITIAL + elapsed  SPEED_RAMP_RATE, SPEED_MAX);
```

`gameSpeed` is applied to all entity `speedX` values and the background
scroll rate. The spawn interval also decreases proportionally:
```javascript
spawnInterval = Math.max(SPAWN_INTERVAL_BASE / gameSpeed, SPAWN_INTERVAL_MIN);
```

---

## 15. Special Events

### Booster Separation -- Score Milestone 1000
- Triggers once only
- Animation: a booster section detaches from Starship bottom and falls away
  over 2 seconds
- Player gains 3 seconds of invincibility
- Brief speed burst (+0.3 to gameSpeed, normalises over 1 second)
- HUD message: "STAGE SEPARATION -- GO, STARSHIP!"

### Solar Storm -- Every 90 Seconds
- Screen overlays a pulsing orange/red wash at 40% opacity
- Lasts 3 seconds
- Obstacle spawn rate increases by 30% during storm
- HUD warning: "[sun] SOLAR STORM -- NAVIGATE BLIND!"

### Starlink Constellation Bonus -- 5 Consecutive Starlinks
- 2x score multiplier for 10 seconds
- Starlink satellites briefly glow gold
- HUD: "* CONSTELLATION COMPLETE -- 2x SCORE!"

### Mars Approach -- Score Milestone 5000
- Background begins transitioning from starfield to reddish Mars horizon
- Transition takes 30 seconds (gradual alpha blend)
- At score 8000: full Mars surface visible -- atmosphere, rust-red ground
- HUD: " MARS APPROACH -- ALMOST THERE!"

---

## 16. HUD (Heads-Up Display)

Drawn on the canvas each frame above all game entities:

```
[SCORE: 1,234]    [DISTANCE: 23% TO MARS]    [[heart] [heart] [empty heart]]
```

| Element | Position | Style |
|---|---|---|
| Score | Top-left | White, 20px, monospace |
| Distance to Mars | Top-centre | White, 16px |
| Lives | Top-right | [heart] icons (filled = alive, outline = lost) |
| Speed multiplier | Top-right below lives | Grey, 12px, "1.8x" |
| Event messages | Centre, below midline | Yellow, 18px, fade out over 2s |
| Floating score text | At collectible position | White/yellow, fades up and out |

---

## 17. Screen Designs

### Title Screen
- Black background with animated starfield
- Starship logo/silhouette (Canvas drawn)
- Title: "SPACEX: MISSION TO MARS" (large, white)
- Subtitle: "Help Elon reach Mars. Don't hit the Roadster."
- Prompt: "PRESS SPACE OR CLICK TO LAUNCH"
- Subtle "Built with DevCortex" attribution bottom-right

### Countdown Screen
- Starfield background continues
- Large centre countdown: 3 -> 2 -> 1 -> LAUNCH!
- Each number fades in and out over 1 second

### Game Over Screen
- Starfield background (or Mars if reached)
- "MISSION FAILED" (red) or "MISSION COMPLETE" (gold) if score >= 8000
- Final score
- Distance reached ("You made it 34% of the way to Mars")
- Best score this session
- "PRESS R OR CLICK TO TRY AGAIN"

### Pause Screen
- Game canvas frozen (no update, render still draws current state)
- Semi-transparent overlay
- "PAUSED -- PRESS P TO RESUME"

---

## 18. Audio (Web Audio API -- optional, graceful degradation)

All sounds synthesised via Web Audio API oscillators -- no audio files:

| Sound | Trigger | Synthesis |
|---|---|---|
| Thruster | While thrust key held | Low-frequency oscillator, sawtooth wave |
| Collision | Player hit | Short noise burst, pitch drop |
| Pickup | Collectible collected | Short sine wave ascending tone |
| Roadster appearance | Tesla Roadster spawns | Brief major chord |
| Booster separation | Stage separation event | Whoosh + ascending tone |
| Solar storm | Event start | Low drone, dissonant |

If `AudioContext` is unavailable or user has not interacted with the page,
audio silently degrades -- no errors, no prompts.

---

## 19. Performance Targets

| Metric | Target |
|---|---|
| Frame rate | 60fps on hardware from 2018 or newer |
| Input latency | < 16ms (one frame) from key/click to visual response |
| File size | < 100KB total |
| Load time | < 1 second on a 10Mbps connection |
| Memory | No memory leaks -- inactive entities purged each frame |

---

## 20. Browser Compatibility

| Browser | Support |
|---|---|
| Chrome 90+ | Primary -- fully supported |
| Edge 90+ | Fully supported |
| Firefox 90+ | Fully supported |
| Safari 15+ | Supported (Web Audio API available) |
| Mobile Chrome/Safari | Supported -- touch controls, landscape orientation |
