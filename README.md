#

<img width="1919" height="995" alt="Screenshot 2026-09-06 200358" src="https://github.com/user-attachments/assets/36286a8b-053c-4653-8194-80659e2d9cbd" />
### 🎮 Short Video of Gameplay

[▶️ **Watch Gameplay Video**]
<video controls width="800">
  <source src="https://raw.githubusercontent.com/Vivekgithubb/Dangerous-Dave_Bhai/main/video1.mp4" type="video/mp4">
</video>



---
Delver Dan: Caverns of Peril
A browser-based retro PC platformer recreation built as a **single
self-contained HTML file** using only **HTML, CSS, and vanilla
JavaScript**.

The project is designed around a deliberately low-resolution 320×200
canvas, pixel-art rendering, synthesized retro audio, tile-based levels,
arcade-style scoring, limited lives, hazards, collectibles, enemies, and
stage transitions.

> **Project goal:** reproduce the feel of an authentic early-era PC
> platformer while keeping the implementation lightweight, portable, and
> dependency-free.

---

## Overview

**Delver Dan: Caverns of Peril** is a five-level side-scrolling platform
game.

The core objective is simple:

1.  Enter the cavern.
2.  Explore the level.
3.  Collect the golden trophy/relic.
4.  Survive hazards and enemies.
5.  Reach the exit door.
6.  Advance through all five stages.
7.  Finish with the highest possible score.

The game starts on a title screen and progresses through
`TITLE → PLAYING → WARPING → PLAYING`, eventually reaching `VICTORY` or
`GAME_OVER`.

The entire game is contained in one HTML document, including:

- HTML structure
- CSS styling
- Canvas rendering
- Game state
- Physics
- Collision detection
- Level data
- Enemy logic
- Bullet logic
- Particle effects
- Audio synthesis
- Keyboard input
- Score/high-score persistence

The game uses a fixed 320×200 internal resolution and applies
nearest-neighbor/pixelated rendering so that scaling the game up does
not destroy the intended pixel-art appearance.

---

## How It Works

### 1. Game Initialization

The browser loads the HTML document and creates a 320×200 `<canvas>`.

The game initializes:

- the audio system
- palette/constants
- level definitions
- player state
- game state
- keyboard state
- high score from `localStorage`

The initial state is `TITLE`.

The canvas is then resized according to the available browser window
while preserving the original aspect ratio.

---

### 2. Game Loop

The game uses `requestAnimationFrame()` for the main loop.

Conceptually:

```text
requestAnimationFrame
        │
        ▼
   gameLoop()
        │
        ├── updatePlayer()
        ├── updateEnemies()
        ├── updateBullets()
        ├── updateParticles()
        │
        ▼
      render()
        │
        ├── Title
        ├── Gameplay
        ├── Warp transition
        ├── Game Over
        └── Victory
        │
        ▼
requestAnimationFrame(...)
```

Gameplay updates only occur while the game is in the `PLAYING` state.
Rendering continues so title, victory, game-over, and transition screens
can animate independently.

---

## Architecture

The implementation is intentionally organized into logical engine
sections even though everything lives in one HTML file.

### High-Level Architecture

```text
Single HTML File
│
├── CSS / Presentation
│   ├── Page layout
│   ├── Canvas scaling
│   ├── Pixel rendering
│   └── CRT scanline effect
│
├── Audio Engine
│   └── RetroAudio
│       ├── Oscillator beeps
│       ├── Noise effects
│       ├── Jump sound
│       ├── Collect sound
│       ├── Trophy sound
│       ├── Warp sound
│       ├── Shooting sound
│       ├── Explosion sound
│       └── Jetpack sound
│
├── Game Configuration
│   ├── PALETTE
│   ├── Tile constants
│   └── Level definitions
│
├── Game State
│   ├── TITLE
│   ├── PLAYING
│   ├── WARPING
│   ├── GAME_OVER
│   └── VICTORY
│
├── Entities
│   ├── Player
│   ├── Enemies
│   ├── Bullets
│   └── Particles
│
├── Gameplay Systems
│   ├── Level loading
│   ├── Physics
│   ├── Collision detection
│   ├── Hazards
│   ├── Collectibles
│   ├── Weapons
│   └── Enemy movement
│
├── Rendering
│   ├── Tiles
│   ├── Player
│   ├── Enemies
│   ├── Particles
│   ├── HUD
│   └── Screens/transitions
│
└── Input / Browser Integration
    ├── Keyboard events
    ├── Responsive canvas scaling
    ├── Window resize
    └── localStorage high score
```

---

## Level Architecture

Levels are represented as compact ASCII-style tile maps.

A character in the map corresponds to a particular tile or game object.

Examples:

Character Meaning

---

`1` Brick / solid wall
`L` Log / platform
`3` Pipe
`W` Water
`F` Fire
`S` Spikes
`V` Climbable vine
`D` Exit door
`Y` Trophy / relic
`B` Blue gem
`R` Red gem
`P` Purple gem
`C` Crown
`J` Jetpack
`G` Ray-gun

This makes level design easy to edit without requiring an external level
editor or asset pipeline.

Each level also stores:

- name
- column count
- tile map
- player spawn
- door location
- trophy location
- enemy configuration

The project currently contains five stages:

1.  **The Lost Entrance**
2.  **The Sunken Sewer**
3.  **The Magma Caverns**
4.  **Flight Test Depot**
5.  **The Guarded Vault**

The level maps were deliberately structured around short staircase-like
platform sequences. The source comments specify a target of no more than
two tiles of vertical rise and two tiles of horizontal gap for
individual jumps.

---

## Gameplay Systems

### Player Physics

The player uses lightweight arcade physics rather than a physics
library.

Current parameters include:

```text
Acceleration : 0.4
Maximum speed: 1.0
Friction     : 0.75
Jump force   : -4.8
Gravity      : 0.28
```

The implementation also includes:

- horizontal acceleration
- friction
- gravity
- jump buffering
- coyote time
- variable jump height
- ladder/vine climbing
- jetpack movement
- facing direction
- walking animation state

Jump buffering and coyote time were included specifically to make the
controls more forgiving than raw collision-based platforming.

---

### Collision Detection

The game uses axis-aligned bounding-box collision and tile-based
collision checks.

Solid tiles are:

- bricks
- logs
- pipes

Hazard tiles are:

- water
- fire
- spikes

The player is checked against the relevant tiles around its bounding box
instead of testing every tile in the entire level.

This keeps the collision system simple and appropriate for a small 2D
game.

---

### Collectibles

Collectibles provide both progression and score.

Item Score / Effect

---

Blue gem +100
Red gem +200
Purple gem +500
Trophy +1000
Crown +2000
Exit door +1500

The trophy is the primary progression item. A player cannot successfully
use the exit door until the trophy has been collected.

The HUD changes from:

```text
>> SECURE THE GOLDEN RELIC <<
```

to:

```text
>> DOOR OPEN! ESCAPE! <<
```

after obtaining the trophy.

---

### Enemies

Two enemy types are currently implemented:

- **Bat**
- **Spider**

Bats move horizontally while also applying a sinusoidal vertical
movement to create a floating effect.

Spiders patrol between configured horizontal boundaries.

Enemies can:

- collide with the player
- cause player death
- be destroyed by ray-gun bullets

Enemy destruction awards additional score.

---

### Ray-Gun

The ray-gun is an optional gameplay mechanic.

After collecting the gun:

- the player receives 8 rounds
- `X` or `CTRL` fires
- bullets travel in the direction the player is facing
- bullets disappear when they hit solid tiles or leave the level
- enemy kills award score

This creates a simple risk/reward system: ammunition is limited, so
firing should be deliberate.

---

### Jetpack

The jetpack provides temporary aerial movement.

When collected:

- fuel is set to 100
- holding the jump/up input consumes fuel
- the player receives continuous upward thrust
- exhaust particles are generated
- a fuel meter appears in the HUD

The jetpack is therefore both a mobility mechanic and a visual feedback
element.

---

## Controls

### Keyboard

Input Action

---

`←` / `A` Move left
`→` / `D` Move right
`↑` / `W` / `Space` Jump / climb / use jetpack
`↓` / `S` Climb down
`X` / `CTRL` Fire ray-gun
`R` Retry current level
`Enter` Start / restart / return to title

The control mapping is intentionally redundant: both arrow keys and WASD
are supported so the game works naturally for different player
preferences.

---

## Scoring and Lives

The game starts with three displayed lives.

Score is accumulated through:

- gems
- trophies
- crowns
- enemy kills
- successful level exits

The high score is stored using browser `localStorage` under:

```text
delver_dan_hi
```

This means the high score survives page reloads on the same
browser/device.

---

# UI / UX Decisions

## 1. Low-Resolution Canvas

The internal resolution is intentionally fixed at:

```text
320 × 200
```

This is one of the most important visual decisions in the project.

Rather than creating modern high-resolution pixel art, the game renders
at a low resolution and scales it up. This produces harder pixel edges
and a more authentic retro computer-game appearance.

---

## 2. Pixelated Rendering

The canvas uses:

```css
image-rendering: pixelated;
image-rendering: crisp-edges;
```

This prevents browser interpolation from blurring the pixels when the
canvas is enlarged.

The goal is not modern visual smoothness. The goal is deliberate pixel
preservation.

---

## 3. CRT / Scanline Effect

A scanline overlay is applied above the game using a CSS pseudo-element.

The effect introduces:

- horizontal scanlines
- subtle RGB channel variation
- dark CRT-style contrast

This was chosen because the visual identity depends heavily on the
feeling of an old display rather than simply using pixelated graphics.

---

## 4. Limited Color Palette

The project uses a deliberately restricted palette containing:

- black
- red
- blue
- green
- cyan
- magenta
- yellow
- white
- brown
- gray variants

Individual gameplay elements use distinct colors so the player can
recognize objects quickly.

For example:

- yellow/gold = objective/reward
- cyan = information/energy
- red = danger
- magenta = enemies/special items
- white/gray = mechanical objects

This is both a visual and usability decision.

---

## 5. Minimal HUD

The bottom 40 pixels of the 200-pixel canvas are reserved for the status
bar.

The HUD communicates:

- score
- lives
- current level
- current objective
- jetpack fuel
- gun/ammunition
- high score

The objective text changes contextually instead of requiring a separate
instruction window.

This keeps the player focused on the game world.

---

## 6. Immediate Feedback

Important actions trigger multiple feedback channels.

For example, collecting the trophy produces:

- score increase
- HUD message
- trophy sound
- sparkle particles
- removal of the trophy from the map

This principle is repeated for:

- gems
- crown
- weapon pickup
- jetpack pickup
- enemy destruction
- player death
- level completion

The intention is to make important actions feel responsive even without
complex animation assets.

---

# Visual Importance

The visual layer is treated as part of the gameplay rather than
decoration.

### Important visual principles

**Pixel clarity**

Every object is drawn using simple canvas primitives. Bricks, logs,
pipes, gems, trophies, weapons, enemies, and the player are all
constructed directly with rectangles and simple paths.

**Contrast**

Dark backgrounds allow important gameplay objects to stand out.

**Color semantics**

Colors communicate function quickly.

**Animation**

Small animations are used selectively:

- trophy bobbing
- animated water
- flickering fire
- walking frames
- bat wing movement
- spider leg movement
- particles
- jetpack exhaust

The project avoids unnecessary continuous effects that would distract
from gameplay.

---

# Audio Design

No external sound files are required.

The project synthesizes its sound effects using the browser's Web Audio
API.

The `RetroAudio` class generates effects using:

- oscillators
- gain envelopes
- frequency sweeps
- generated noise

Implemented effects include:

- jump
- gem collection
- trophy collection
- stage warp
- ray-gun firing
- enemy explosion
- player hit/death
- jetpack thrust

This was an important architectural choice because it preserves the
**single-file / zero-dependency** requirement while still providing game
audio.

---

# Technical Challenges

## 1. Making Pixel Art Without External Assets

Instead of loading sprite sheets or image files, the game draws the
visuals procedurally using Canvas 2D primitives.

This makes the project:

- portable
- self-contained
- easy to submit
- easy to run locally
- independent of asset paths

The trade-off is that more code is required for visual construction.

---

## 2. Responsive Scaling

The game needs to remain visually faithful while running in different
browser window sizes.

The solution is to preserve the internal 320×200 resolution and
calculate an integer scale factor based on available window dimensions.

This allows:

```text
320 × 200
640 × 400
960 × 600
1280 × 800
...
```

without introducing fractional scaling.

---

## 3. Platforming Feel

Platformers are highly sensitive to movement parameters.

Small changes in:

- acceleration
- maximum horizontal speed
- gravity
- jump force
- friction

can make a game feel either sluggish or uncontrollably fast.

The current physics were therefore tuned around a deliberately low
maximum movement speed and relatively controlled jumping.

Jump buffering and coyote time were added to reduce frustrating missed
inputs.

---

## 4. Level Difficulty

The levels use hand-authored tile layouts instead of procedural
generation.

This gives precise control over:

- jump spacing
- platform placement
- collectible locations
- hazard placement
- enemy placement
- progression

The challenge is maintaining difficulty without creating jumps that are
technically possible but practically frustrating.

This is especially important in later stages, where hazards, enemies,
and traversal mechanics overlap.

---

## 5. Browser Audio Restrictions

Modern browsers commonly require user interaction before an
`AudioContext` can begin playing audio.

The project therefore initializes/resumes audio from keyboard
interaction rather than attempting to start audio automatically when the
page loads.

This keeps the game compatible with browser autoplay restrictions.

---

# Design Decisions

## Why Vanilla JavaScript?

A framework would add unnecessary complexity for a small canvas game.

Vanilla JavaScript provides:

- direct browser API access
- zero build process
- zero dependencies
- easy local execution
- easy submission

---

## Why Canvas?

Canvas is a natural fit for a 2D game because it allows direct control
over:

- pixel placement
- animation
- sprite-like procedural drawings
- camera movement
- effects
- screen transitions

It also avoids creating hundreds of DOM elements for individual tiles.

---

## Why Tile-Based Levels?

Tile-based levels provide deterministic collision and straightforward
editing.

A designer can visually understand a map string and modify level
geometry without touching the physics engine.

---

## Why a State Machine?

The game has multiple distinct modes:

```text
TITLE
PLAYING
WARPING
GAME_OVER
VICTORY
```

Using explicit states prevents gameplay logic from running during menus
and transitions.

It also makes the rendering flow easier to reason about.

---

## Why Procedural Audio?

External audio files would break the single-file design.

Synthesized effects provide enough feedback for:

- jumping
- collecting
- firing
- dying
- winning
- transitioning

without adding external dependencies.

---

# Current Errors / Known Limitations

The current implementation is functional, but there are several areas
that should be treated as known limitations.

### 1. Frame-Rate-Dependent Physics

Movement and physics values are updated once per animation frame rather
than using a delta-time-based simulation.

This means gameplay behavior can vary slightly between very different
refresh rates or under heavy frame drops.

**Improvement:** move physics calculations to a fixed timestep or
multiply motion by elapsed time.

---

### 2. Life Counter Edge Case

The current death logic decrements `lives` and checks whether it is
below zero. Because the initial value is `3`, the implementation can
effectively allow one more death than the displayed three-life concept
suggests.

**Improvement:** use a clear convention such as:

```js
lives--;

if (lives <= 0) {
  gameState = STATE.GAME_OVER;
}
```

---

### 3. Audio Object Creation During Jetpack Use

The jetpack sound can be triggered repeatedly while the player holds the
control.

Because the sound implementation creates Web Audio nodes for each
effect, prolonged jetpack use can generate many short-lived audio nodes.

**Improvement:** use a persistent jetpack oscillator or throttle sound
generation.

---

### 4. No Dedicated Pause System

There is currently no explicit pause state or pause control.

**Improvement:** add:

```text
PAUSED
```

to the state machine and bind it to `Escape` or `P`.

---

### 5. No Mobile / Touch Controls

The current control system is keyboard-oriented.

**Improvement:** add an optional virtual control layer for touch devices
without changing the underlying game engine.

---

### 6. Limited Enemy Variety

Only bats and spiders are currently implemented.

**Improvement:** introduce additional enemy behaviors such as:

- stationary turrets
- ground patrol enemies
- projectiles
- vertical enemies
- enemies with different attack patterns

---

### 7. Limited Level Count

There are currently five handcrafted stages.

The level data structure is designed so additional stages can be added
without changing the core engine.

---

# Improvements / Future Work

## Gameplay

- Improve difficulty curve between stages.
- Add more enemy types.
- Add more complex enemy behavior.
- Add checkpoints.
- Add secret areas.
- Add additional weapon types.
- Add more collectible combinations.
- Add bonus stages.
- Add timed challenges.

## Physics

- Convert movement to delta-time or fixed-timestep physics.
- Further tune jump arcs.
- Improve slope/edge handling if slopes are introduced.
- Add more robust collision resolution.

## UI / UX

- Add pause menu.
- Add mute/audio controls.
- Add dedicated control/settings screen.
- Add better game-over feedback.
- Add level completion statistics.
- Add accessibility-oriented control options.

## Visuals

- Expand the procedural sprite library.
- Add more environmental tile variants.
- Add richer background layers.
- Add controlled parallax.
- Add more transition effects.
- Add more character animation frames.

## Audio

- Add looping background music.
- Add separate volume controls.
- Reuse audio nodes for continuous sounds.
- Add stage-specific sound themes.

## Technical

- Split the single HTML file into modules for maintainability.
- Add automated level validation.
- Add unit tests for collision and scoring.
- Add a debug mode with collision boxes.
- Add deterministic fixed-timestep simulation.
- Add developer tools for quickly testing individual levels.

---

# Running the Project

No installation or build process is required.

Simply open the HTML file in a modern browser.

```text
Pasted code(2).html
```

The game should run directly from the browser.

Because the project uses browser APIs such as Canvas, Web Audio,
keyboard events, window resizing, and `localStorage`, it is intended for
a modern desktop browser.

---

# Project Structure

Although the implementation is currently a single file, the logical
structure is:

```text
/
└── index.html
    ├── <style>
    │   └── UI / CRT presentation
    │
    ├── <canvas>
    │   └── 320×200 game surface
    │
    └── <script>
        ├── RetroAudio
        ├── Palette
        ├── Tile definitions
        ├── Level definitions
        ├── Game state
        ├── Player
        ├── Level loader
        ├── Collision
        ├── Physics
        ├── Enemies
        ├── Bullets
        ├── Particles
        ├── Rendering
        ├── HUD
        ├── Screen transitions
        ├── Input
        └── Main loop
```

---

# Development Philosophy

The project prioritizes **game feel and visual authenticity over
technical complexity**.

The guiding principles are:

1.  Keep the game immediately playable.
2.  Preserve a low-resolution pixel aesthetic.
3.  Avoid unnecessary dependencies.
4.  Make levels deterministic and hand-tunable.
5.  Give every important player action clear feedback.
6.  Keep the implementation portable enough to run from a single HTML
    file.
7.  Prefer simple systems that are easy to understand and modify.

---

# Summary

Delver Dan: Caverns of Peril is a compact demonstration of how a
complete retro-style platformer can be implemented using only
browser-native technologies.

The project combines:

- HTML
- CSS
- JavaScript
- Canvas 2D
- Web Audio API
- localStorage
- requestAnimationFrame

into a self-contained game engine.

The most important design choice is the combination of a **320×200
rendering surface, procedural pixel-art graphics, synthesized audio,
tile-based level design, simple arcade physics, and state-driven
gameplay**.

This keeps the project technically lightweight while still providing the
visual, audio, and interaction cues needed for a convincing retro
platformer experience.
