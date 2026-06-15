# Concept of Operations (ConOps)
## SpaceX Mission to Mars -- Browser Runner Game

Document Version: 1.0
Status: Draft
Author: Mark Ward (MainRobin Pty Ltd)
*Date: June 2026

---

## 1. Background and Purpose

This document describes the operational concept for SpaceX Mission to Mars, a
browser-based side-scrolling runner game built as a public demonstration project
for DevCortex (devcortexai.com).

The game serves two purposes:

1. *Entertainment** -- a fun, replayable, instantly accessible browser game with
   a SpaceX theme that non-technical users can enjoy without installation
2. **DevCortex demonstration** -- a concrete example of a complete software project
   specified, implemented, and verified using the DevCortex Agentic V-Model
   methodology, published on GitHub so developers can inspect the requirements,
   trace matrix, and AI agent audit trail

---

## 2. Vision

A single-file browser game (index.html) requiring no installation, no account,
and no backend. The player pilots Starship through increasingly hostile deep space
on a mission to Mars, dodging debris and collecting fuel while the universe does
its best to stop them.

The game is thematically grounded in the real SpaceX mission narrative --
Starship, booster separation, Starlink constellations, the famous Tesla Roadster
-- with a tongue-in-cheek layer of humour that makes it shareable.

---

## 3. Users and Context

Primary users: Developers and engineering teams evaluating DevCortex -- they
will encounter the game via the DevCortex GitHub demo repository or a direct
GitHub Pages link shared on LinkedIn, Hacker News, or Reddit.

Secondary users: General public who enjoy casual browser games -- the game
should be approachable and immediately playable without reading any instructions.

Environment: Desktop browser (Chrome, Edge, Firefox, Safari) on any operating
system. Touch and mobile support is desirable but secondary to desktop gameplay.

Session length: 1 to 5 minutes per play. High replayability is more important
than depth.

---

## 4. Operational Concept

### 4.1 The Journey

The player controls Starship on its journey from Earth orbit to Mars. The ship
scrolls automatically from left to right (the mission is always moving forward).
The player controls vertical position only -- thruster bursts move the ship up;
gravity and inertia pull it down. The mission ends if the ship collides with an
obstacle or runs out of lives.

### 4.2 Game Flow

**1. Title Screen** -- press spacebar or click to begin

**2. Countdown** -- 3... 2... 1... Launch!

**3. Gameplay Loop* (repeats until lives exhausted)
- Obstacles spawn and scroll left
- Collectibles spawn and scroll left
- Score accumulates per distance travelled
- Speed gradually increases
- Periodic events: solar storms, booster separation, NASA bureaucracy
- Collision: life lost, brief invincibility, then resume
- Lives exhausted: transition to Game Over

*4. Game Over Screen**
- Final score displayed
- Distance to Mars shown as percentage of journey
- Play Again option

### 4.3 Core Systems

**Game Loop and Delta Time**
A continuous requestAnimationFrame loop updates all game state. Delta time
normalises movement speed across different hardware -- a player on a slow machine
experiences identical gameplay to a player on a fast machine.

**Procedural Spawning**
Pre-designed obstacle and collectible patterns are selected randomly and spawned
just off the right edge of the canvas. Entities that pass off the left edge are
removed from memory. This creates an effectively infinite level without manual
design.

**Collision Detection**
Axis-aligned bounding box (AABB) collision testing checks the player hitbox
against every active obstacle each frame. A collision immediately decrements the
player lives and triggers a brief invincibility window.

**Progressive Difficulty (Velocity Ramp)**
A global speed multiplier increases continuously over time, accelerating both
the background scroll and all obstacle movement. The player feels mounting
pressure the longer they survive.

### 4.4 Controls

| Input | Action |
|---|---|
| Spacebar (hold) | Thruster burst -- ship moves up |
| Left mouse button (hold) | Thruster burst -- ship moves up |
| Touch (hold) | Thruster burst -- ship moves up |
| Release | Gravity pulls ship down |
| P | Pause / resume |
| R | Restart (from Game Over screen) |

### 4.5 Obstacles

| Obstacle | Description | Behaviour |
|---|---|---|
| Space debris | Jagged grey chunks | Fixed vertical position, varying speed |
| Dead satellites | Old comm satellites | Slow, large hitbox |
| Space rocks | Asteroid fragments | Randomised vertical drift |
| Tesla Roadster | Elons 2018 Starman payload | Rare, fast, plays a sound |
| NASA paperwork wall | Bureaucratic hazard | Full-height gap, player finds opening |

### 4.6 Collectibles

| Collectible | Effect |
|---|---|
| Fuel canister | +100 score |
| Starlink satellite | +200 score; collect 5 in a row for multiplier burst |
| Dragon capsule | +1 life (max 3) |
| X post (Elon tweet) | Temporary obstacle speed reduction (market chaos) |

### 4.7 Special Events

| Event | Trigger | Effect |
|---|---|---|
| Booster separation | Score milestone 1000 | Animation + brief invincibility + speed burst |
| Solar storm | Every 90 seconds approx | Screen flares orange, reduced visibility for 3s |
| Starlink constellation | 5 Starlinks collected consecutively | 2x score multiplier for 10s |
| Mars approach | Score milestone 5000 | Background shifts from starfield to Mars horizon |

### 4.8 Scoring

- Distance score: +1 per frame survived
- Collectible bonuses: per collectible table above
- Speed bonus: score multiplier scales with current game speed
- Distance to Mars: displayed as a percentage of the total journey (0% to 100%)

---

## 5. Key Constraints

| Constraint | Detail |
|---|---|
| Delivery format | Single index.html file -- no build step, no dependencies |
| Runtime dependencies | None -- vanilla JavaScript and Canvas API only |
| Deployment | GitHub Pages -- static file hosting, no backend |
| Asset loading | All assets drawn inline using Canvas API |
| Browser support | Chrome 90+, Edge 90+, Firefox 90+, Safari 15+ |
| Mobile | Touch controls supported; optimised for landscape orientation |
| File size | Under 100KB total |

---

## 6. Out of Scope (v1)

- Leaderboards or score persistence across sessions
- User accounts or authentication
- Multiplayer
- Native mobile app
- Level editor
- Unlockable content or progression systems
- Server-side anything

---

## 7. Success Criteria

The game is considered successful when:

1. A user can open index.html in a browser and begin playing within 5 seconds with no installation or setup
2. The game is immediately understandable without reading instructions
3. A single playthrough lasts between 1 and 5 minutes at medium skill level
4. The game remains playable and enjoyable for at least 5 replays
5. The SpaceX theme is immediately recognisable and the humour lands
6. The GitHub repository demonstrates a complete DevCortex project with full requirement traceability from spec to verified implementation

---

## 8. DevCortex Integration

This project is built using the DevCortex Agentic V-Model:

- Requirements and acceptance criteria are defined in DevCortex before implementation begins
- An AI coding agent (Claude Code) implements the game guided by the DevCortex MCP server
- Each acceptance criterion is formally verified by the agent and recorded in the DevCortex trace matrix
- The resulting audit trail is published alongside the game as evidence of the methodology

The GitHub repository structure reflects this:

- index.html -- the complete game
- README.md -- play instructions and DevCortex story
- docs/ConOps.md -- this document
- docs/Spec.md -- technical specification
- docs/requirements.md -- requirements with acceptance criteria
- .devcortex-link.json -- links repo to DevCortex project
