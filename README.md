# Delver Dan: Caverns of Peril

## Overview

**Delver Dan: Caverns of Peril** is a browser-based retro platform game implemented as a single HTML file using **HTML5 Canvas, CSS, and vanilla JavaScript**.

The supplied implementation is built around a low-resolution **320×200 Canvas**, procedural pixel-art rendering, keyboard controls, platform physics, collectible treasures, enemies, a jetpack, a ray-gun, hazards, and DOS-inspired screen transitions.

The source identifies the title as **“Delver Dan: Caverns of Peril”** and creates the game using a `320×200` Canvas. fileciteturn1file0L1-L5 fileciteturn1file0L83-L86

---

# 1. Project Purpose

The project is intended to reproduce the feel of an early DOS-style arcade/platform game inside a modern web browser.

The main design goals are:

- Low-resolution pixel graphics.
- A deliberately restricted retro color palette.
- Keyboard-driven gameplay.
- Canvas-based game rendering.
- Procedurally generated graphics.
- Synthesized retro sound effects.
- Hand-authored levels.
- Simple game-state driven progression.
- No conventional game framework.

The Canvas is explicitly configured for crisp pixel rendering through `image-rendering: pixelated` and `image-rendering: crisp-edges`. fileciteturn1file0L36-L42

---

# 2. Technology Stack

The implementation uses browser-native technology.

## HTML

HTML supplies:

- the document
- the game container
- the Canvas
- the instruction bar
- the JavaScript code

The main framebuffer is:

```html
<canvas id="canvas" width="320" height="200"></canvas>
```

This gives the game a fixed internal rendering resolution. fileciteturn1file0L83-L86

## CSS

CSS is responsible for:

- centering the game
- black background
- pixelated Canvas scaling
- retro presentation
- instruction bar styling
- scanline-style overlay

The source applies `pixelated` and `crisp-edges` rendering directly to the Canvas. fileciteturn1file0L36-L42

## JavaScript

JavaScript implements the complete game engine:

```text
Audio
Level data
Level loading
Player
Physics
Collision
Enemies
Bullets
Collectibles
Particles
Rendering
HUD
Transitions
Game states
```

The main implementation is contained in the `<script>` element and runs in strict mode. fileciteturn1file0L98-L100

---

# 3. Overall Architecture

The program can be viewed as these major systems:

```text
                     ┌─────────────────────┐
                     │     GAME STATE      │
                     └──────────┬──────────┘
                                │
              ┌─────────────────┼─────────────────┐
              │                 │                 │
              ▼                 ▼                 ▼
        INPUT SYSTEM       GAME UPDATE        RENDERING
              │                 │                 │
              │       ┌─────────┼─────────┐       │
              │       │         │         │       │
              │       ▼         ▼         ▼       │
              │    PLAYER    ENEMIES    BULLETS    │
              │       │         │         │       │
              │       └─────────┼─────────┘       │
              │                 ▼                 │
              │            COLLISIONS             │
              │                 │                 │
              │          ┌──────┴──────┐          │
              │          ▼             ▼          │
              │      HAZARDS      COLLECTIBLES    │
              │                        │           │
              └────────────────────────┼───────────┘
                                       ▼
                                  HUD / STATUS
```

This separation makes it possible to change one system without rewriting the rest of the game.

---

# 4. Canvas and Coordinate System

The internal resolution is:

```text
320 × 200 pixels
```

The tile size is:

```text
16 × 16 pixels
```

There are:

```text
10 tile rows
```

The source defines these constants directly:

```javascript
const WIDTH = 320;
const HEIGHT = 200;
const TILE = 16;
const ROWS = 10;
```

and derives the playfield size from those values. fileciteturn1file0L240-L244

The result is a classic low-resolution game framebuffer that can then be enlarged using CSS without smoothing.

---

# 5. Retro Display

The Canvas is intentionally rendered at low resolution and enlarged only for display.

The CSS uses:

```css
image-rendering: pixelated;
image-rendering: crisp-edges;
```

This prevents interpolation from turning individual game pixels into blurry modern graphics. fileciteturn1file0L36-L42

The page itself uses a black background and centers the game vertically and horizontally. fileciteturn1file0L14-L23

---

# 6. Palette

A named palette is declared near the beginning of the JavaScript.

The source includes:

```text
BLACK
BLUE
GREEN
CYAN
RED
MAGENTA
BROWN
LGRAY
DGRAY
LBLUE
LGREEN
LCYAN
LRED
LMAGENTA
YELLOW
WHITE
```

It also adds specialized colors for:

- bricks
- wood/logs
- HUD borders
- gold
- highlights
- backgrounds

The palette is defined in the `PALETTE` object. fileciteturn1file0L210-L237

This lets the graphics functions refer to semantic colors rather than hard-coding every value.

---

# 7. Level Data

Levels are stored in the `LEVELS` array.

Each level is an object containing fields such as:

```javascript
{
    name: "...",
    cols: 40,
    map: [...],
    spawns: {...},
    door: {...},
    trophy: {...},
    enemies: [...]
}
```

The source comments explicitly describe the level set as **handcrafted staircase maps**. fileciteturn1file0L265-L267

The supplied file contains five authored levels in this structure. fileciteturn1file0L267-L416

---

# 8. ASCII Map System

The maps are represented as text strings.

For example:

```text
1111111111111111111111111111111111111111
1......................................1
1....LLL...............................1
1...................................D..1
1111111111111111111111111111111111111111
```

Each character represents a tile or game object.

This makes the level layout easy to read and edit.

It also separates:

```text
LEVEL DESIGN
```

from:

```text
ENGINE CODE
```

A designer can change platforms or collectibles without modifying the physics engine.

---

# 9. Tile Encoding

During `loadLevel()`, the map characters are converted into numeric tile IDs.

The supplied implementation maps:

| Character | Meaning |
|---|---|
| `1` | Brick |
| `L` | Log/platform |
| `3` | Pipe |
| `W` | Water |
| `F` | Fire |
| `S` | Spikes |
| `V` | Climbable vine |
| `D` | Door |
| `Y` | Trophy |
| `B` | Blue gem |
| `R` | Red gem |
| `P` | Purple gem |
| `C` | Crown |
| `J` | Jetpack |
| `G` | Gun |

The conversion is performed in the level-loading switch statement. fileciteturn1file0L505-L560

---

# 10. Level Loading

`loadLevel()` is the central initialization function.

It is responsible for:

1. Selecting the requested level.
2. Calculating the level width.
3. Creating the runtime tile grid.
4. Restoring trophy state.
5. Resetting the player.
6. Creating enemies.
7. Clearing bullets.
8. Clearing particles.
9. Updating the HUD.

The source performs these tasks in `loadLevel()`. fileciteturn1file0L488-L611

This is important because the same function can be used after:

- changing levels
- dying
- restarting
- completing the game

---

# 11. Player Entity

The player is represented by the `Player` class.

The player stores:

```text
x
y
width
height
vx
vy
facing
onGround
climbing
hasTrophy
jetpackFuel
hasGun
ammo
isDead
deathTimer
animation frame
animation timer
coyote time
jump buffer
```

These variables are initialized in `Player.reset()`. fileciteturn1file0L453-L480

The collision box is separate from the graphical representation, allowing the artwork to be larger or differently shaped without changing collision behavior.

---

# 12. Player Physics

The player's behavior is handled by `updatePlayer()`.

The current implementation uses:

```javascript
ACCEL = 0.4;
MAX_SPEED = 1.0;
FRICTION = 0.75;
JUMP_FORCE = -4.8;
GRAVITY = 0.28;
```

These values are applied continuously during player updates. fileciteturn1file0L722-L726

The result is a conventional:

```text
horizontal acceleration
        +
friction
        +
vertical velocity
        +
gravity
```

model.

---

# 13. Horizontal Movement

When LEFT is pressed:

```javascript
player.vx = Math.max(player.vx - ACCEL, -MAX_SPEED);
```

When RIGHT is pressed:

```javascript
player.vx = Math.min(player.vx + ACCEL, MAX_SPEED);
```

When neither direction is active, velocity is reduced using friction.

This is explicitly implemented in the player update routine. fileciteturn1file0L722-L739

The player is therefore not moved by directly adding a constant speed to `x` every browser frame.

---

# 14. Jump System

The jump begins by assigning a strong negative vertical velocity:

```javascript
player.vy = JUMP_FORCE;
```

After that, gravity gradually increases the vertical velocity until the player begins falling. fileciteturn1file0L767-L786

The current implementation also contains:

```text
coyoteTime
jumpBuffer
variable jump height
```

These are held in:

```javascript
player.coyoteTime
player.jumpBuffer
```

and are used to make jump input more forgiving. fileciteturn1file0L772-L790

---

# 15. Variable Jump Height

The source reduces upward velocity when UP is released:

```javascript
if (!keys.up && player.vy < -2.0 && !usingJetpack) {
    player.vy *= 0.55;
}
```

This means the player can influence jump height by controlling the jump input. fileciteturn1file0L790-L791

This is a deliberate implementation feature of the supplied version.

---

# 16. Climbing

The player detects whether the center of their body overlaps a climbable tile.

The relevant tiles are represented by the vine/climbable tile type.

While climbing:

```text
UP   → move upward
DOWN → move downward
```

and gravity is suspended.

The supplied source implements this behavior around the center-tile check and climbing logic. fileciteturn1file0L756-L768

---

# 17. Jetpack

The jetpack is activated when the player has fuel and presses UP.

The current implementation applies upward thrust:

```javascript
player.vy = -2.5;
```

while fuel is continuously consumed. fileciteturn1file0L739-L754

Jetpack exhaust particles can also be generated while flying.

When fuel reaches zero, the normal gravity system becomes active again.

---

# 18. Collision Detection

The fundamental collision system uses **Axis-Aligned Bounding Boxes (AABB)**.

The overlap function is:

```javascript
function boxOverlap(a, b) {
    return (
        a.x < b.x + b.w &&
        a.x + a.w > b.x &&
        a.y < b.y + b.h &&
        a.y + a.h > b.y
    );
}
```

This is the basic test used for entity collisions. fileciteturn1file0L644-L650

---

# 19. Tile Collision Resolution

The player first moves horizontally, then vertically.

Horizontal processing:

```text
Calculate new X
      ↓
Find touched tiles
      ↓
Check whether tile is solid
      ↓
If blocked:
    snap player to wall
    zero horizontal velocity
```

Vertical processing:

```text
Calculate new Y
      ↓
Find touched tiles
      ↓
Check whether tile is solid
      ↓
If landing:
    snap player onto surface
    zero vertical velocity
    mark onGround
```

The source explicitly performs separate X and Y collision passes. fileciteturn1file0L807-L844

---

# 20. Solid Tiles

The current engine treats the following tile types as solid:

```javascript
T.BRICK
T.LOG
T.PIPE
```

This is defined by `isSolid()`. fileciteturn1file0L636-L638

This means logs and pipes can function as physical structures rather than only decorative tiles.

---

# 21. Hazard Detection

Hazards include:

```text
Water
Fire
Spikes
```

The helper function `isHazard()` identifies these tile types. fileciteturn1file0L640-L642

The hazard routine calculates the player's nearby tiles and constructs a reduced hazard hitbox.

That helps avoid making every pixel of a 16×16 tile instantly lethal. fileciteturn1file0L653-L678

---

# 22. Enemy System

Enemies are created from the `enemies` array attached to each level.

Enemy data can include:

```text
type
x
y
minX
maxX
speed
```

During level loading, these are converted into runtime enemy objects. fileciteturn1file0L583-L598

The main enemy update routine then changes their position and animation.

---

# 23. Enemy Types

The supplied implementation contains enemy rendering/behavior for:

```text
Bat
Spider
```

Bats flap between their horizontal movement limits and use an animated vertical movement component.

Spiders patrol horizontally.

The behavior is controlled by `updateEnemies()`. fileciteturn1file0L970-L985

---

# 24. Shooting System

The player can obtain a ray-gun.

When the weapon is available and the fire input is activated:

```text
Create bullet
      ↓
Give it horizontal velocity
      ↓
Move every update
      ↓
Check solid tiles
      ↓
Check enemy collision
```

The source creates projectile objects with position, size, and velocity. fileciteturn1file0L794-L805

Bullets are updated separately in `updateBullets()`. fileciteturn1file0L1002-L1013

---

# 25. Enemy Destruction

When a bullet overlaps an enemy:

```text
Enemy destroyed
Bullet removed
Explosion particles created
Score increased
Explosion sound played
```

The supplied code performs this inside `updateEnemies()`. fileciteturn1file0L984-L996

This keeps enemy damage handling separate from player movement.

---

# 26. Collectibles

The game contains multiple collectible items.

The internal tile system supports:

```text
Trophy
Blue Gem
Red Gem
Purple Gem
Crown
Jetpack
Gun
```

During the player update, nearby tiles are checked.

When a collectible is found:

```text
Tile changed to EMPTY
        ↓
Score increases
        ↓
Sound plays
        ↓
Particle effect
        ↓
Player state updated
```

The corresponding logic is inside the tile-processing section of `updatePlayer()`. fileciteturn1file0L883-L959

---

# 27. Score Values

The supplied implementation assigns the following score values:

| Collectible | Score |
|---|---:|
| Blue Gem | 100 |
| Red Gem | 200 |
| Purple Gem | 500 |
| Crown | 2000 |
| Trophy | 1000 |
| Enemy | 300 |
| Door completion | 1500 |

These values are applied directly in the collection and enemy routines. fileciteturn1file0L891-L995

---

# 28. Trophy / Door Progression

The trophy is the main level objective.

When it is collected:

```javascript
player.hasTrophy = true;
```

The HUD changes to indicate that the player should reach the door. fileciteturn1file0L891-L899

When the player reaches the door:

```javascript
if (player.hasTrophy) {
    score += 1500;
    startWarpTransition(currentLevelIdx + 1);
}
```

Otherwise the door remains locked and the player receives an explanatory message. fileciteturn1file0L948-L959

---

# 29. Death System

The player can die from hazards or enemy collisions.

`killPlayer()`:

1. Sets `isDead`.
2. Starts a death timer.
3. Plays a hit sound.
4. Generates red/yellow particles.

The source creates a large particle burst for the death effect. fileciteturn1file0L694-L700

The player then waits through the death timer instead of being instantly reset.

---

# 30. Respawning

After the death timer expires:

```text
Lives decrease
      ↓
If lives remain:
    reload level
Else:
    GAME OVER
```

The supplied implementation performs this inside `updatePlayer()`. fileciteturn1file0L703-L719

The level is loaded again using the centralized level-loading routine.

---

# 31. Game State Machine

The current source declares:

```javascript
const STATE = {
  TITLE: 0,
  PLAYING: 1,
  WARPING: 2,
  GAME_OVER: 3,
  VICTORY: 4
};
```

This gives the program a single global game mode. fileciteturn1file0L417-L425

The high-level flow is:

```text
TITLE
  ↓
PLAYING
  ↓
WARPING
  ↓
NEXT LEVEL
  ↓
PLAYING
  ↓
...
  ↓
VICTORY
```

Death can lead to either:

```text
PLAYING
```

after a respawn, or:

```text
GAME OVER
```

when all lives are gone.

---

# 32. Level Transition

Levels do not switch immediately.

`startWarpTransition()` changes the state to:

```javascript
STATE.WARPING
```

and starts the transition sequence. fileciteturn1file0L614-L620

The transition renderer uses multiple horizontal shutter-like strips.

The sequence is:

```text
Close screen
     ↓
Display transition/intermission message
     ↓
Load next stage
     ↓
Open screen
     ↓
Resume gameplay
```

The supplied implementation explicitly calls this process from `renderWarpTransition()`. fileciteturn1file0L1252-L1313

---

# 33. Retro Audio

The sound system is implemented by the `RetroAudio` class.

The class lazily creates an `AudioContext` and resumes it if the browser has suspended it. fileciteturn1file0L102-L118

This is necessary because modern browsers commonly restrict audio playback until the user interacts with a page.

---

# 34. Oscillator-Based Sound

The `beep()` method creates:

```text
OscillatorNode
+
GainNode
```

The oscillator can:

- change waveform
- start at a frequency
- sweep to another frequency
- fade its volume

This makes it possible to approximate simple PC-speaker-style effects without audio files. fileciteturn1file0L119-L142

---

# 35. Noise-Based Effects

The `noise()` method creates an audio buffer containing generated random samples.

This is useful for:

```text
explosions
impact sounds
noise bursts
```

The implementation creates the buffer dynamically using the browser's `AudioContext`. fileciteturn1file0L144-L169

---

# 36. Available Sound Effects

The source implements dedicated routines for:

```text
playJump()
playGem()
playTrophy()
playWarp()
playShoot()
playExplode()
playHit()
playJet()
```

These effects are synthesized instead of loaded from MP3/WAV files. fileciteturn1file0L171-L205

---

# 37. Player Animation

The player renderer changes the artwork according to:

```text
idle
walking
climbing
jumping
falling
shooting
jetpack use
```

The player's animation state is maintained in:

```javascript
animFrame
animTick
```

and updated during movement. fileciteturn1file0L959-L968

This allows the character to look animated without using sprite sheets.

---

# 38. Procedural Graphics

Environmental objects are drawn entirely from Canvas primitives.

Examples:

```javascript
drawBrick()
drawLog()
drawPipe()
drawWater()
drawFire()
drawSpikes()
drawDoor()
```

The renderer uses rectangles and simple geometric shapes instead of external image files. fileciteturn1file0L1025-L1107

Collectibles are similarly generated:

```javascript
drawTrophy()
drawGem()
drawCrown()
drawJetpack()
drawGun()
```

fileciteturn1file0L1108-L1165

---

# 39. Player Rendering

`drawPlayer()` builds the character out of simple Canvas rectangles.

The character includes separate regions representing:

```text
shirt
face
body
legs
feet
weapon
jetpack
```

The player's facing direction is also used so the weapon and character orientation can change. fileciteturn1file0L1166-L1210

---

# 40. Enemy Rendering

`drawEnemy()` supports different visual representations depending on enemy type.

### Bat

The bat includes:

- body
- eyes
- flapping wings

### Spider

The spider includes:

- body
- eyes
- animated legs

The renderer is driven from the enemy's `type` and `animTick`. fileciteturn1file0L1213-L1247

---

# 41. Camera

The game uses a horizontal camera because the level width is larger than the 320-pixel viewport.

The level width is calculated with:

```javascript
levelWidth = lvl.cols * TILE;
```

For a 40-column level:

```text
40 × 16 = 640 pixels
```

The camera is positioned relative to the player and clamped to the level boundaries. fileciteturn1file0L496-L499 fileciteturn1file0L854-L856

---

# 42. World Rendering

`renderGameWorld()`:

1. Clears the playfield.
2. Applies the camera translation.
3. Determines visible columns.
4. Iterates through the tile grid.
5. Draws the appropriate tile.
6. Draws bullets.
7. Draws enemies.
8. Draws the player.
9. Draws particles.

This creates the complete gameplay scene before the HUD/status bar is rendered. fileciteturn1file0L1318-L1400

---

# 43. HUD / Status Bar

The current supplied version uses a lower Canvas status bar.

It displays:

```text
SCORE
LIVES
LEVEL
JETPACK FUEL
GUN AMMO
HIGH SCORE
```

and context-sensitive messages.

The implementation is contained in `renderStatusBar()`. fileciteturn1file0L1402-L1478

This allows the gameplay information to remain visually integrated with the game rather than being ordinary web UI.

---

# 44. On-Screen Instructions

The HTML page additionally shows a small instruction bar beneath the Canvas.

The supplied instructions are:

```text
UP              JUMP / CLIMB / JETPACK
LEFT / RIGHT    MOVE
DOWN            CLIMB DOWN
X / CTRL        FIRE RAY-GUN
R               RETRY LEVEL
```

This is defined in the HTML outside the game Canvas. fileciteturn1file0L88-L95

---

# 45. High Score System

The implementation stores a high score using browser `localStorage`.

The key is:

```javascript
delver_dan_hi
```

The high score is loaded when the page starts and written back when a new high score is achieved. fileciteturn1file0L426-L432 fileciteturn1file0L707-L714

Because it uses `localStorage`, the score can persist across browser reloads on the same origin.

---

# 46. Particles

Particles are represented by small runtime objects containing:

```text
x
y
vx
vy
color
life
```

Each update changes position and decreases lifetime.

The particles are removed once their lifetime expires. fileciteturn1file0L681-L692

They are mainly used for:

```text
Treasure pickup
Enemy destruction
Death
Jetpack effects
```

---

# 47. Performance Model

The rendering path is intentionally straightforward:

```text
Canvas
  ↓
Tile loop
  ↓
Entities
  ↓
Particles
```

The source avoids building a conventional DOM element for every game object.

The actual gameplay world is rendered directly using Canvas APIs.

This is appropriate for a small 320×200 game.

---

# 48. Running the Game

The project is intended to run directly in a browser.

Basic workflow:

```text
1. Save the HTML file.
2. Open it in Chrome, Edge, or Firefox.
3. Interact with the page to enable audio.
4. Use the keyboard controls.
```

No compilation stage is necessary for the browser-side JavaScript shown in the supplied file.

---

# 49. Development Workflow

A practical workflow for modifying the game is:

### Modify a level

Edit the relevant `LEVELS` entry.

### Modify gameplay physics

Adjust constants inside `updatePlayer()`.

### Modify player graphics

Edit `drawPlayer()`.

### Modify environmental graphics

Edit functions such as:

```text
drawBrick()
drawLog()
drawPipe()
drawWater()
drawFire()
drawSpikes()
drawDoor()
```

### Modify sound

Edit the methods in `RetroAudio`.

### Modify scoring

Change the score additions in the collectible and enemy logic.

### Modify game progression

Change the state transitions around:

```text
loadLevel()
startWarpTransition()
killPlayer()
```

---

# 50. Source Structure by Section

The supplied code is already organized into logical numbered sections:

```text
1. Retro Audio Synthesis
2. Palette & Constants
3. Handcrafted Level Maps
4. Engine State
5. Player Entity
6. Level Initialization / Physics / Collision
7. Enemies / Bullets / Particles
8. Graphics Rendering
```

This structure makes the large HTML file easier to navigate. fileciteturn1file0L102-L210 fileciteturn1file0L417-L488

---

# 51. Important Implementation Characteristics

A few characteristics of the current implementation are particularly important for anyone extending it.

## Physics are acceleration-based

The current player movement is not purely instantaneous. It uses acceleration, a maximum horizontal speed, and friction. fileciteturn1file0L722-L739

## Jump assistance exists

The supplied implementation includes both:

```text
coyote time
jump buffering
```

which are usually associated with more forgiving modern platformers. fileciteturn1file0L772-L786

## Variable jump height exists

Releasing UP can shorten the jump. fileciteturn1file0L790-L791

## Levels are handcrafted

The level definitions are manually authored rather than procedurally generated. fileciteturn1file0L265-L267

## The visual palette is custom

The source is retro-styled but uses its own named color palette, including colors such as:

```text
#0000CC
#00D800
#EE0000
#FFEE00
```

rather than a strict canonical EGA palette. fileciteturn1file0L210-L237

---

# 52. Complete Runtime Flow

The complete game can be understood as:

```text
PAGE LOAD
   │
   ▼
INITIALIZE CANVAS
   │
   ▼
INITIALIZE GAME STATE
   │
   ▼
TITLE SCREEN
   │
   │ Enter
   ▼
LOAD LEVEL
   │
   ▼
PLAYING
   │
   ├── Read keyboard state
   ├── Update player
   ├── Resolve collisions
   ├── Check hazards
   ├── Collect items
   ├── Update enemies
   ├── Update bullets
   ├── Update particles
   ├── Check door
   │
   ├───────────────┐
   │               │
   ▼               ▼
DEATH             DOOR + TROPHY
   │               │
   ▼               ▼
RESPAWN          WARP
   │               │
   └───────┐       ▼
           │   NEXT LEVEL
           │       │
           └───────┘
                   │
                   ▼
                VICTORY
```

---

# 53. Why the Architecture Works

The biggest benefit of the current design is that the game is not tied to a browser-specific UI framework.

Canvas handles:

```text
Graphics
HUD
World
Characters
Enemies
Particles
Transitions
```

JavaScript handles:

```text
Rules
Physics
Input
State
Audio
Collision
Progression
```

CSS handles:

```text
Page layout
Canvas scaling
Pixel rendering
Retro presentation
```

That separation is enough for a small standalone browser game.

---

# 54. Possible Future Improvements

The current code provides a complete playable framework, but several areas can be further refined depending on the project's goal.

### Authenticity

The physics can be tuned against reference recordings of the original game.

### Graphics

The procedural shapes can be replaced by more accurate pixel sprite tables while keeping the same single-file architecture.

### Audio

The oscillator frequencies and timings can be tuned to more closely reproduce period-specific PC-speaker effects.

### Level Design

The handcrafted staircase maps can be extended with additional enemy patterns, traps, and alternate routes.

### Engine

The physics, rendering, and entity systems can be separated into additional modules if the project later stops requiring a single-file build.

---

# 55. Summary

**Delver Dan: Caverns of Peril** is implemented as a compact browser game engine rather than as a collection of independent web pages.

The central pipeline is:

```text
Keyboard
   ↓
Game State
   ↓
Player / Physics
   ↓
Tile + Entity Collision
   ↓
Enemies / Bullets
   ↓
Collectibles / Hazards
   ↓
Level Progression
   ↓
Canvas Renderer
   ↓
Retro Audio
```

The result is a self-contained low-resolution Canvas game with:

- 320×200 rendering
- pixelated scaling
- handcrafted level maps
- procedural tile graphics
- procedural character/enemy graphics
- AABB collision
- player physics
- climbing
- jetpack mechanics
- ray-gun shooting
- collectibles
- hazards
- enemy AI
- death and respawn
- score/high-score tracking
- synthesized audio
- warp transitions
- title, gameplay, game-over and victory states

The supplied source contains these systems in one HTML document, making it straightforward to run, inspect, modify, or submit as a standalone browser-game project.
