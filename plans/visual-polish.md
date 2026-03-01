# Visual Polish for GravityProto

## Context
The game currently has flat-colored circle planets (all look the same except size/color), a plain circle spaceship, and white-dot-only stars. The goal is to make planets visually diverse and 3D-looking, add space atmosphere (nebulae, colored stars), and improve the ship design -- all without causing lag.

**Only file modified**: `gravityproto/game.js`

---

## 1. Color Utilities (prerequisite for everything)
- [x] Add `hexToHSL(hex)`, `hslToString(h,s,l)`, and `colorWithAlpha(color, alpha)` helper functions near the top of the file

## 2. Planet Visual Overhaul (biggest impact)

### 2a. Expand planet data at generation time
- [x] In `generateChunkBodies()`, add visual properties to each body object: `planetType` (0-4), `featureSeed`, `hasRing`, `ringAngle`, `atmosphereOpacity`
- [x] Use a **separate sub-RNG** (`mulberry32(chunkSeed(cx,cy) * 31 + i * 7919)`) for visual properties so existing planet positions/sizes are completely unchanged

### 2b. Pre-render planets to offscreen canvases
- [x] New function `renderPlanetToCache(body)` -- creates an offscreen canvas, draws the planet with all detail, stores on `body.cachedCanvas`
- [x] Called lazily on first render; cached canvas is garbage-collected when chunk is evicted

### 2c. Planet rendering layers (drawn to offscreen canvas):
- [x] **Outer glow** -- soft radial gradient in planet color
- [x] **3D sphere** -- radial gradient offset upper-left (light source), going from light to dark shade of base color
- [x] **Surface features** (clipped to planet circle), one of 5 types per planet:
  - Type 0: **Banded gas giant** -- 4-7 horizontal bands with slight hue variation
  - Type 1: **Cratered rocky** -- 3-8 darker circles on surface
  - Type 2: **Spotted/volcanic** -- 2-5 bright hotspots
  - Type 3: **Smooth ringed** -- subtle bands (always gets a ring)
  - Type 4: **Swirled/stormy** -- 2-4 arc patterns
- [x] **Atmosphere rim** -- thin bright stroke at planet edge
- [x] **Rings** -- elliptical rings for type 3 + 15% of others, using `ctx.ellipse()` with 2-3 concentric strokes

### 2d. Update drawMass
- [x] Replace current gradient-per-frame rendering with `ctx.drawImage(m.cachedCanvas, ...)` -- actually *faster* than current approach
- [x] Increase `viewMargin` from 100 to 150 to accommodate ringed planets

## 3. Background Overhaul

### 3a. Deep space gradient
- [x] Replace flat `#0a0a0f` fill with a subtle radial gradient that shifts hue slightly based on camera position

### 3b. Colored stars
- [x] Add a `STAR_COLORS` table (white-heavy with some blue, yellow, orange, red tints)
- [x] Each star picks a color from the table via its tile RNG

### 3c. Star sparkle effect
- [x] For bright stars (brightness > 0.85), draw a small cross/ray pattern -- 4 tiny `fillRect` calls

### 3d. Nebula clouds
- [x] New tile-based system at 0.1x parallax (behind stars)
- [x] Per-tile: 0-2 large radial gradient blobs in blues/purples/magentas at very low opacity (0.03-0.06)
- [x] Pre-rendered to offscreen canvas tiles, cached in a `nebulaTileCache` Map (max 25 tiles)

### 3e. Render order update
- [x] Background gradient -> Nebulae -> Stars -> World objects -> HUD

## 4. Spaceship Improvements

- [x] Pre-render ship to offscreen canvas: octagonal body with radial gradient (white center fading to cyan), thin edge stroke, center dot
- [x] Separate crashed variant (red tones)
- [x] Add subtle pulsing shield aura (`Math.sin(Date.now() * 0.004)`) -- one cheap gradient per frame
- [x] Enhanced exhaust: larger radius (8px), add white-hot inner core gradient

## Performance Notes
- Planet caching makes rendering **faster** than current (drawImage vs per-frame gradient creation)
- Nebula tiles cached to offscreen canvases, evicted at 25 tile limit (~25MB max)
- Planet canvases GC'd naturally with chunk eviction
- Star color adds 1 rng() call and string template per star -- negligible
- Ship pulse is 1 gradient per frame -- negligible

## Verification
- [x] Run in browser, navigate around and confirm planets look diverse (different types, rings, 3D shading)
- [x] Check performance stays smooth (no visible stuttering)
- [x] Verify nebulae/colored stars visible in background
- [x] Confirm ship looks nicer with octagonal shape and pulse
- [x] Playwright browser test to screenshot and verify rendering
