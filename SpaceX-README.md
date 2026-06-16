# SpaceX: Mission to Mars

A browser-based side-scrolling runner game. Pilot Starship through increasingly hostile deep space on a mission to Mars -- dodge debris, collect fuel, and try not to hit Elon's Roadster.

No installation. No account. No dependencies. Just open and play.

**[Play Now](https://mainrobin.github.io/spacex-mission-to-mars/)** | **[Built with DevCortex](https://www.devcortexai.com)**

---

## Play

Open `index.html` in any browser. That's it.

**Or play online:** [mainrobin.github.io/spacex-mission-to-mars](https://mainrobin.github.io/spacex-mission-to-mars/)

Compatible with Chrome, Edge, Firefox, and Safari on desktop and mobile.

---

## How to Play

### Objective

Pilot Starship from Earth orbit to Mars. The ship flies forward automatically -- your only job is to control its vertical position and survive long enough to reach the red planet.

### Controls

| Input | Action |
|---|---|
| Spacebar (hold) | Fire thrusters -- move up |
| Left mouse button (hold) | Fire thrusters -- move up |
| Touch and hold (mobile) | Fire thrusters -- move up |
| Release | Gravity pulls the ship down |
| P | Pause / resume |
| R | Restart (from Game Over screen) |

### Survival Tips

- Hold thrust in short bursts -- holding continuously sends you straight into the top wall
- The gap in the NASA Paperwork Wall is always passable -- look for it early and adjust your altitude
- Collect Starlinks in a row -- five consecutive Starlinks triggers a 2x score multiplier
- The Tesla Roadster moves fast -- it appears rarely but crosses the screen quickly; if you hear the chord, react immediately
- X Posts slow everything down -- grab them when surrounded by fast-moving debris

### Obstacles

| Obstacle | Notes |
|---|---|
| Space Debris | Jagged chunks at varying heights and speeds |
| Dead Satellites | Slow but large -- don't underestimate the hitbox |
| Space Rocks | Drift slightly up and down as they cross |
| Tesla Roadster | Rare, fast, leaves a particle trail. Yes, that's Starman in the driver seat. |
| NASA Paperwork Wall | Full-height barrier with a gap -- find the opening and thread it |

### Collectibles

| Collectible | Effect |
|---|---|
| Fuel Canister | +100 score |
| Starlink Satellite | +200 score -- collect 5 in a row for 2x multiplier for 10 seconds |
| Dragon Capsule | +1 life (maximum 3) |
| X Post | Slows all obstacles for 5 seconds (market chaos ensued) |

### Special Events

**Booster Separation** -- at 1,000 points, the first stage detaches. Brief invincibility and a speed burst. "Go, Starship!"

**Solar Storm** -- every 90 seconds or so, the screen flares orange and visibility drops for 3 seconds. Spawn rate increases. Stay calm.

**Starlink Constellation** -- collect 5 Starlinks without interruption. Score multiplier activates for 10 seconds.

**Mars Approach** -- past 5,000 points the starfield starts shifting red. At 8,000 points you see the Martian horizon. Mission complete.

### Scoring

- Distance score: +1 per frame survived
- Speed bonus: the faster the game runs, the faster your score climbs
- Collectible bonuses: per table above
- Distance to Mars: shown as a percentage (0% to 100%) -- 100% = score 8,000
- Mission Complete: reach 8,000 points to land on Mars

### Lives

You start with 3 lives shown as heart icons top-right. Each collision costs one life and triggers a 2-second invincibility window. Lose all three and it is Mission Failed.

---

## Screenshots

![Title Screen](docs/screenshots/title.png)
![Gameplay](docs/screenshots/gameplay.png)
![Mars Approach](docs/screenshots/mars-approach.png)

---

## Technical Details

The entire game is a single `index.html` file -- no build step, no dependencies, no backend.

| Item | Detail |
|---|---|
| Language | Vanilla JavaScript (ES2020) |
| Rendering | HTML5 Canvas 2D API |
| Audio | Web Audio API (synthesised -- no audio files) |
| Storage | None -- no data is saved or transmitted |
| File size | Under 100KB |
| Dependencies | None |

All visuals are drawn with Canvas 2D primitives. All audio is synthesised via Web Audio API oscillators -- no sound files are loaded. The game makes zero network requests during play.

---

## Run Locally

No build step required:

```bash
git clone https://github.com/mainrobin/spacex-mission-to-mars
cd spacex-mission-to-mars
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

Or just double-click `index.html` in your file manager.

---

## Browser Compatibility

| Browser | Status |
|---|---|
| Chrome 90+ | Fully supported |
| Edge 90+ | Fully supported |
| Firefox 90+ | Fully supported |
| Safari 15+ | Fully supported |
| Mobile Chrome/Safari | Supported -- landscape orientation recommended |

Run remotely https://game.devcortexai.com

---

## Built with DevCortex

This game was built as a public demonstration project for [DevCortex](https://www.devcortexai.com) -- a requirements and verification layer for AI-assisted software development.

### What DevCortex does

AI coding agents write code quickly but have no structured definition of "done." DevCortex closes this gap by sitting between your planning tools and your AI coding agents, providing:

- Structured requirements with formal acceptance criteria, served to agents via an MCP server
- Verification tracking -- agents record PASS or FAIL against each AC after implementation
- A trace matrix -- every requirement linked to its ACs, implementation evidence, and verification status
- An audit trail -- every agent action timestamped and attributed

### How this game was built

SpaceX: Mission to Mars was fully specified in DevCortex before a single line of code was written:

- ConOps -- concept of operations: the game vision, player experience, and success criteria
- Spec -- technical specification: game systems, constants, entity definitions, collision model, audio approach
- 12 requirements, 87 acceptance criteria -- every feature and constraint formally specified in SHALL/WHEN/THE format
- 1 build sprint -- implemented by Claude Code as the AI coding agent, guided by the DevCortex MCP server
- All 87 ACs verified -- each acceptance criterion formally tested and recorded in the DevCortex trace matrix

The result is a fully traceable project where every feature links back to a requirement, every requirement has verified acceptance criteria, and the agent's implementation evidence is captured in the audit trail.

### The Agentic V-Model

DevCortex operationalises a lightweight systems engineering framework for AI-assisted development:

- Left side: Requirements and Acceptance Criteria -- specify what to build
- Middle: AI Agent Implementation
- Right side: Verification and Trace Matrix -- prove it was built right

The left side specifies. The right side verifies. The trace matrix connects them.

### Try DevCortex

- Website: devcortexai.com
- Free tier available -- no credit card required
- CLI: npm install -g @devcortex/cli
- MCP server connects Claude Code, Cursor, Cline, OpenCode and other agents to your requirements backlog

---

## Project Structure

```
spacex-mission-to-mars/
    index.html          The complete game (open this to play)
    README.md           This file
    docs/
        ConOps.md       Concept of operations
        Spec.md         Technical specification
        requirements.md Requirements and acceptance criteria
        screenshots/    Game screenshots
```

---

## Contributing

Bug reports and pull requests are welcome.

If you find an issue, open a new issue with your browser version, OS, and steps to reproduce.

---

## Licence

MIT -- see LICENSE for details.

---

SpaceX: Mission to Mars -- Built with DevCortex (devcortexai.com) -- MainRobin Pty Ltd
