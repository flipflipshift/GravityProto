# GravityProto — Prototype V3: Infinite Scrolling World

## Implementation Steps

### 1. Camera System & New Constants
- [x] Add constants: `CHUNK_SIZE = 400`, `ACTIVE_RADIUS = 4`, `WORLD_SEED = 42`, `SPAWN_ZONE_R = 200`
- [x] Add `camera` object `{ x, y }` tracking viewport top-left in world coords
- [x] Add scoring state: `maxDistFromOrigin`, `highScore`

### 2. Chunk-Based Procedural Generation
- [x] Add `mulberry32(seed)` — seeded PRNG (deterministic random per chunk)
- [x] Add `chunkSeed(cx, cy)` — hash function combining chunk coords with world seed
- [x] Add `generateChunkBodies(cx, cy)` — produces 0-2 bodies per chunk
- [x] Add `chunkCache` Map + `getChunkBodies(cx, cy)` for caching
- [x] Add `getActiveBodies()` — collects bodies from all chunks within `ACTIVE_RADIUS` of camera center

### 3. Refactor Physics for Infinite World
- [x] `computeAcceleration(px, py, activeBodies)` — accept bodies array param
- [x] `updatePhysics()` — use active bodies, remove bounds check, add camera + scoring

### 4. Refactor Rendering with Camera Transform
- [x] `render()` — use `ctx.save() / ctx.translate(-camera.x, -camera.y)` for world-space drawing

### 5. Spawn at Origin
- [x] Rewrite `spawnRocket()` — rocket spawns at world origin `(0, 0)`

### 6. Remove Body Selector, Add Scoring
- [x] Remove `PRESETS`, `masses`, `activeBodyCount`, `setBodyCount()`, `window.masses`
- [x] Remove from HTML: `#body-selector` div and its CSS
- [x] Update `crash()` — record high score
- [x] Add `drawScore()` — show distance and high score
- [x] Start game with `spawnRocket()` + `loop()`

### 7. Visual Polish
- [x] Add `drawStarfield()` — deterministic parallax stars
- [x] Add `drawMinimap()` — small corner overlay
- [x] Update page title to "Gravity Explorer"

### 8. Performance Optimization
- [x] Compute `getActiveBodies()` once per frame (shared via `cachedActiveBodies`)
- [x] Bumped `MAX_SPEED` to 9.0 for infinite world feel

## Verification
- [x] Rocket spawns at origin in empty safe zone
- [x] Moving in any direction reveals procedural bodies
- [x] Returning to a visited area shows identical bodies (deterministic)
- [x] Bodies grow larger/denser farther from origin
- [x] Collision causes crash, score records distance
- [x] High score persists across respawns within session
- [x] Star field provides visual motion feedback
- [x] Minimap shows nearby bodies and origin direction
- [x] Fuel system + infinite fuel toggle still work
- [x] Run Playwright verification to confirm UI renders correctly
