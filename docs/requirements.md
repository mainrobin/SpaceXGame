# Requirements Document
## SpaceX Mission to Mars -- Browser Runner Game

Document Version: 1.0
Status: Draft
Author: Mark Ward (MainRobin Pty Ltd)
Date: June 2026

---

## Glossary

- **Game Loop** -- the continuous `requestAnimationFrame` cycle that updates and renders the game
- **Delta Time** -- elapsed time in seconds since the last frame, used to normalise movement speed
- **Entity* -- any moving game object: obstacle or collectible
- *Obstacle** -- an entity that ends a life on collision with the player
- **Collectible** -- an entity that applies a beneficial effect when collected
- **Game Speed** -- the global speed multiplier applied to all entity movement and background scroll
- **HUD** -- Heads-Up Display; score, lives, and distance shown on the canvas
- **AABB** -- Axis-Aligned Bounding Box; the collision detection method used
- **Invincibility Window** -- a brief period after a collision during which the player cannot lose another life
- **Hitbox* -- the collision rectangle for the player, set to 80% of the visible sprite

---

## Requirements

---

### Requirement 1: Game Loop and Delta Time

**User Story:** As a player, I want the game to run smoothly at a consistent speed regardless of my computer's performance, so that the experience is fair and enjoyable on any hardware.

#### Acceptance Criteria

1. THE game SHALL use `requestAnimationFrame` as the basis for the game loop.
2. EACH frame SHALL compute a `deltaTime` value equal to the elapsed time in seconds since the previous frame.
3. `deltaTime` SHALL be capped at a maximum of 0.05 seconds (equivalent to 20fps) to prevent large position jumps when the tab is backgrounded or the browser throttles the loop.
4. ALL entity positions and velocities SHALL be updated by multiplying their per-second rates by `deltaTime`, ensuring consistent movement speed across different frame rates.
5. THE game loop SHALL continue running in all game states; only the `update` function is gated by state -- `render` always executes.
6. THE game SHALL maintain 60fps on hardware from 2018 or newer running Chrome, Edge, or Firefox.

---

### Requirement 2: Player Control (Starship)

**User Story:** As a player, I want to control Starship's vertical position using thruster bursts so that I can navigate through obstacles on my way to Mars.

#### Acceptance Criteria

1. THE player's Starship SHALL be fixed at a horizontal position of approximately 150px from the left edge of the canvas.
2. WHILE the spacebar, left mouse button, or touch is held, THE Starship SHALL accelerate upward at the defined thrust rate.
3. WHEN the spacebar, left mouse button, or touch is released, THE Starship SHALL decelerate upward and accelerate downward under gravity.
4. THE Starship SHALL not move above a minimum y-position of 20px from the top edge of the canvas.
5. THE Starship SHALL not move below a maximum y-position of 20px from the bottom edge of the canvas.
6. THE Starship's vertical velocity SHALL be capped at the defined terminal velocity in both directions.
7. THE player's collision hitbox SHALL be 80% of the visible sprite dimensions, centred on the sprite.
8. WHEN the player is in the invincibility window following a collision, THE Starship SHALL visually flash (alternating opacity) to indicate the invincibility state.
9. THE Starship SHALL be rendered using Canvas 2D primitives only -- no external image files.
10. WHEN thrusters are active, THE Starship SHALL display a visible engine glow or flame effect.

---

### Requirement 3: Procedural Spawning

**User Story:** As a player, I want an effectively infinite level that never repeats itself exactly, so that the game remains fresh and challenging across multiple playthroughs.

#### Acceptance Criteria

1. THE game SHALL spawn entities (obstacles and collectibles) at a defined interval that decreases as game speed increases.
2. ENTITIES SHALL spawn just off the right edge of the canvas and scroll left at a speed derived from the entity's base speed multiplied by the current game speed.
3. WHEN an entity's right edge passes the left edge of the canvas, THE entity SHALL be removed from the active entity array.
4. THE spawning system SHALL select from a set of at least five pre-defined obstacle patterns: SOLO, HIGH_LOW, WAVE, CORRIDOR, and SWARM.
5. PATTERN selection SHALL be random with no guaranteed sequence, ensuring no two play sessions follow the same obstacle order.
6. THE spawning system SHALL produce obstacles 70% of the time and collectibles 30% of the time.
7. THE Tesla Roadster obstacle SHALL have no more than one instance active at any time, with a maximum 5% spawn probability per obstacle roll.
8. THE spawn interval SHALL have a defined minimum floor that prevents entity density from becoming impossibly high at maximum game speed.

---

### Requirement 4: Obstacles

**User Story:** As a player, I want to encounter a variety of obstacles with distinct visual designs and behaviours so that the game remains visually interesting and tactically varied.

#### Acceptance Criteria

1. THE game SHALL include at least five distinct obstacle types: Space Debris, Dead Satellite, Space Rock (Asteroid), Tesla Roadster, and NASA Paperwork Wall.
2. EACH obstacle type SHALL have a distinct visual appearance rendered using Canvas 2D primitives.
3. THE NASA Paperwork Wall SHALL span the full canvas height with a gap through which the player must navigate; gap height SHALL be at least 130px.
4. THE gap position in the Paperwork Wall SHALL be randomised vertically within safe bounds on each spawn.
5. THE Tesla Roadster SHALL move faster than other obstacles and SHALL trigger an audio cue (synthesised chord) when it spawns.
6. Space Debris and Space Rocks SHALL have randomised sizes within defined minimum and maximum bounds.
7. ALL obstacles SHALL move left at a speed equal to their base speed multiplied by the current game speed multiplier.
8. COLLISION between the player's hitbox and any obstacle (while not invincible) SHALL decrement the player's lives by one.

---

### Requirement 5: Collectibles

**User Story:** As a player, I want to collect beneficial items during my mission to earn score bonuses and gain advantages so that there is a positive incentive to manoeuvre, not just survival.

#### Acceptance Criteria

1. THE game SHALL include four collectible types: Fuel Canister, Starlink Satellite, Dragon Capsule, and X Post.
2. COLLECTING a Fuel Canister SHALL add 100 points to the player's score.
3. COLLECTING a Starlink Satellite SHALL add 200 points to the player's score.
4. COLLECTING a Dragon Capsule SHALL add one life to the player's total, up to a maximum of 3 lives.
5. COLLECTING an X Post SHALL reduce the speed of all active obstacles for 5 seconds without affecting the background scroll speed.
6. WHEN a collectible is collected, THE collectible SHALL be removed from the canvas immediately.
7. WHEN a collectible is collected, THE game SHALL display a floating score or effect label at the point of collection that fades upward and disappears over approximately 1 second.
8. WHEN a collectible is collected, THE game SHALL play a brief audio pickup sound.
9. EACH collectible type SHALL have a distinct visual appearance rendered using Canvas 2D primitives.

---

### Requirement 6: Collision Detection

**User Story:** As a player, I want collisions to feel fair and accurate so that I feel in control of my fate and trust the game's outcomes.

#### Acceptance Criteria

1. THE game SHALL perform AABB collision detection between the player's hitbox and every active obstacle each frame.
2. COLLISION detection SHALL use the player's hitbox (80% of sprite size) rather than the full sprite bounds.
3. WHEN a collision is detected and the player is not invincible, THE game SHALL immediately decrement the player's lives by one.
4. FOLLOWING a collision, THE player SHALL be granted an invincibility window of 2 seconds during which further collisions with obstacles are ignored.
5. WHEN a collision occurs, THE canvas SHALL flash red for 200ms.
6. WHEN a collision occurs, THE game SHALL play a collision audio cue.
7. WHEN the player's lives reach zero, THE game SHALL immediately transition to the GAME_OVER state.
8. COLLISION between the player and a collectible SHALL trigger the collectible's effect without affecting the player's lives.

---

### Requirement 7: Progressive Difficulty

**User Story:** As a player, I want the game to get progressively harder the longer I survive so that there is always a sense of mounting challenge and my skill is meaningfully tested.

#### Acceptance Criteria

1. THE game SHALL maintain a global game speed multiplier that begins at 1.0 and increases continuously over time.
2. THE speed multiplier SHALL increase at a rate of 0.04 per second of gameplay.
3. THE speed multiplier SHALL be capped at a maximum of 3.2.
4. THE speed multiplier SHALL apply to all entity movement speeds and the background scroll rate.
5. THE obstacle spawn interval SHALL decrease proportionally as game speed increases, down to a defined minimum floor.
6. THE current speed multiplier SHALL be displayed on the HUD so players can see their current difficulty level.
7. THE speed multiplier SHALL reset to 1.0 when a new game starts.

---

### Requirement 8: Special Events

**User Story:** As a player, I want periodic special events to break up the routine and create memorable moments in my journey to Mars.

#### Acceptance Criteria

1. THE Booster Separation event SHALL trigger once when the player's score reaches 1000 points.
2. DURING Booster Separation, THE game SHALL display an animation of a booster section detaching from Starship and falling away over 2 seconds.
3. DURING Booster Separation, THE player SHALL receive 3 seconds of invincibility and a brief speed burst.
4. THE Solar Storm event SHALL trigger approximately every 90 seconds (+/-20 seconds random variance) while in the PLAYING state.
5. DURING a Solar Storm, THE canvas SHALL display an orange/red overlay at 40% opacity for 3 seconds.
6. DURING a Solar Storm, THE obstacle spawn rate SHALL increase by 30%.
7. THE Starlink Constellation Bonus SHALL trigger when the player collects 5 Starlink Satellites consecutively without collecting any other collectible type or hitting an obstacle.
8. DURING the Starlink Constellation Bonus, THE score multiplier SHALL be 2x for 10 seconds.
9. THE Mars Approach visual transition SHALL begin when the player's score reaches 5000 points.
10. THE background SHALL gradually transition from a starfield to a Mars horizon scene over 30 seconds, completing at score 8000.
11. ALL special events SHALL display a HUD message when triggered that fades out over 2 seconds.

---

### Requirement 9: Scoring and HUD

**User Story:** As a player, I want to see my current score, lives, and distance to Mars at all times so that I understand my progress and can set personal improvement goals.

#### Acceptance Criteria

1. THE game SHALL accumulate score continuously at a rate of 1 point per frame survived.
2. THE score SHALL be increased by collectible bonuses as defined in Requirement 5.
3. THE score SHALL be multiplied by the active multiplier during the Starlink Constellation Bonus.
4. THE HUD SHALL display the current score in the top-left of the canvas at all times during PLAYING and PAUSED states.
5. THE HUD SHALL display the player's remaining lives as heart icons ([heart] filled = alive, [empty heart] outline = lost) in the top-right of the canvas.
6. THE HUD SHALL display the player's distance to Mars as a percentage (0% to 100%) in the top-centre of the canvas.
7. THE HUD SHALL display the current game speed multiplier below the lives display.
8. THE distance to Mars percentage SHALL be calculated proportionally to the score -- 100% corresponds to a score of 8000 points.
9. THE game SHALL track and display the best score achieved in the current browser session on the Game Over screen.

---

### Requirement 10: Game State Screens

**User Story:** As a player, I want clear, informative screens between gameplay states so that I always understand what is happening and how to proceed.

#### Acceptance Criteria

1. THE Title Screen SHALL display the game title, a subtitle, and a clear prompt to begin.
2. THE Title Screen SHALL display an animated starfield background.
3. THE Countdown Screen SHALL display a 3-second countdown (3 -> 2 -> 1 -> LAUNCH!) before gameplay begins.
4. THE Game Over Screen SHALL display "MISSION FAILED" for scores below 8000, or "MISSION COMPLETE" for scores of 8000 or higher.
5. THE Game Over Screen SHALL display the player's final score and distance reached as a percentage.
6. THE Game Over Screen SHALL display the session best score.
7. THE Game Over Screen SHALL provide a clear prompt to restart the game.
8. THE Pause Screen SHALL display a semi-transparent overlay with a resume prompt, while keeping the frozen game canvas visible underneath.
9. ALL screens SHALL be rendered entirely on the canvas -- no HTML overlays.
10. THE Title Screen SHALL include a "Built with DevCortex" attribution linking to devcortexai.com.

---

### Requirement 11: Audio

**User Story:** As a player, I want audio feedback that enhances the gameplay experience without requiring any external sound files to be loaded.

#### Acceptance Criteria

1. ALL audio SHALL be synthesised using the Web Audio API -- no external audio files of any kind.
2. THE game SHALL produce a thruster sound while the thrust input is held.
3. THE game SHALL produce a collision sound when the player hits an obstacle.
4. THE game SHALL produce a pickup sound when the player collects a collectible.
5. THE Tesla Roadster's appearance SHALL trigger a brief synthesised chord.
6. THE Booster Separation event SHALL trigger a synthesised whoosh and ascending tone.
7. THE Solar Storm event SHALL trigger a low drone audio cue at the start of the event.
8. IF the Web Audio API is unavailable or the user has not yet interacted with the page, audio SHALL silently degrade -- no errors, warnings, or prompts SHALL be shown to the user.

---

### Requirement 12: Technical Constraints

**User Story:** As a developer evaluating DevCortex, I want the game to be a simple, self-contained example that demonstrates the methodology without unnecessary complexity.

#### Acceptance Criteria

1. THE entire game SHALL be contained in a single `index.html` file with no external dependencies.
2. THE game SHALL use only vanilla JavaScript (ES2020+) and the HTML5 Canvas 2D API -- no frameworks, libraries, or build tools.
3. THE total file size of `index.html` SHALL not exceed 100KB.
4. THE game SHALL not make any network requests during gameplay.
5. THE game SHALL function correctly when opened directly from the local filesystem (via `file://` protocol) without a web server.
6. THE canvas SHALL scale to fill the browser window while maintaining its aspect ratio, with no scrollbars.
7. THE game SHALL support touch input on mobile browsers, with landscape orientation recommended via a `<meta>` viewport tag.
8. THE game SHALL display correctly and be playable on Chrome 90+, Edge 90+, Firefox 90+, and Safari 15+.
