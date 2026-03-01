# Fix Nebula Tile Seams, Slower Parallax, Darker Planets

## Context
After the visual tuning pass, the user notices the sky sometimes looks "cut into quadrants" — this is caused by nebula blob gradients being hard-clipped at tile boundaries (blobs up to 650px radius rendered on 1024px tiles, but adjacent tiles have uncorrelated blobs so gradients end abruptly at edges). The user also wants even slower background scrolling ("almost imperceptible") and darker planets.

**Only file modified**: `gravityproto/game.js`

---

## 1. Fix Nebula Tile Seams (~line 422)

### Root cause
`renderNebulaTile(tx, ty)` generates blobs only for that tile. A blob centered near an edge gets clipped at the 1024px boundary — the adjacent tile has completely different blobs, creating a hard line.

### Fix: Cross-tile blob bleeding
Extract blob generation into a pure-data function. When rendering a tile, also generate blobs for all 8 neighbors and render any that bleed into the current tile's area.

- [x] Add `generateNebulaBlobs(tx, ty)` — returns array of `{bx, by, br, nc, opacity}` using the existing seeded RNG logic (deterministic per tile coords)
- [x] Rewrite `renderNebulaTile(tx, ty)` to:
  1. Loop over the 3x3 neighborhood `(tx + dx, ty + dy)` where `dx, dy ∈ {-1, 0, 1}`
  2. Call `generateNebulaBlobs(ntx, nty)` for each neighbor
  3. For each blob, compute its position relative to the current tile: `relX = bx + (ntx - tx) * NEBULA_TILE_SIZE`, `relY = by + (nty - ty) * NEBULA_TILE_SIZE`
  4. Skip if the blob circle doesn't overlap the tile area (simple AABB check: `relX + br < 0 || relX - br > NEBULA_TILE_SIZE` etc.)
  5. Render the blob gradient at `(relX, relY)` on the tile canvas
- [x] Clear the `nebulaTileCache` at the end (structure changed, old tiles invalid) — actually not needed since cache is lazy and this is a code change

---

## 2. Even Slower Background Parallax

Current values (from last pass): stars `0.15`, nebula `0.05`.
Target: "almost imperceptible" movement.

- [x] Star parallax: `0.15` → `0.05`
- [x] Nebula parallax: `0.05` → `0.02`

---

## 3. Darker Planets

### 3a. Outer glow (line 636-637)
- [x] Alpha: `0.18` → `0.10`
- [x] Outer radius: `r + pad * 0.5` → `r + pad * 0.35` (tighter glow)

### 3b. 3D sphere highlight (line 648)
- [x] Lightness boost: `+18 (max 80)` → `+10 (max 65)`
- [x] Dark side: `hsl.l - 25` → `hsl.l - 35` (deeper shadow)

### 3c. Surface feature opacities (further reduction)
- [x] Gas giant bands: `0.20-0.35` → `0.12-0.22`
- [x] Crater fill: `0.35-0.55` → `0.20-0.35`, rim: `0.15` → `0.10`
- [x] Volcanic hotspots: center `0.5` → `0.3`, mid `0.3` → `0.15`, globalAlpha `0.45-0.65` → `0.30-0.45`
- [x] Smooth ringed: `0.12-0.20` → `0.08-0.14`
- [x] Stormy arcs: `0.25-0.40` → `0.15-0.25`

### 3d. Atmosphere rim (line 174)
- [x] Opacity: `0.10-0.25` → `0.06-0.15`

---

## Verification
- [ ] Playwright: load game, fly around, screenshot — no visible tile boundary lines in nebulae
- [ ] Background should be nearly stationary when flying
- [ ] Planets should appear darker/more muted against the space background
