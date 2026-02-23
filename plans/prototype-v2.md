# GravityProto — Prototype V2 Plan

## Context
The existing GravityProto is a browser-based gravity sim with 2 hardcoded gravitational bodies, thrust controls, and a fuel system. Prototype V2 adds body count selection, fixes missing refueling, and initializes git. Source: `~/org/projects/gravitygame.org` "Prototype V2" section.

## Files to Modify
- `GravityProto/game.js` — all gameplay changes
- `GravityProto/index.html` — new UI controls
- `GravityProto/.gitignore` — new file

## Steps

### - [x] 1. Git init + .gitignore
- Run `git init` in `GravityProto/`
- Create `.gitignore` (`.DS_Store`, `*.log`, etc.)

### - [x] 2. Add body count selector (1–5) with preset layouts
**UI**: Add a row of 5 buttons (labeled 1–5) above the canvas or in the controls bar. Highlight the active count.

**Preset positions** (centered on 800×600 canvas):

| Count | Layout |
|-------|--------|
| 1 | Single large body at center (400, 300), mass 6000 |
| 2 | Current layout — (250, 300) mass 5000, (580, 300) mass 3000 |
| 3 | Triangle — top-center + bottom-left + bottom-right |
| 4 | Diamond — top, bottom, left, right |
| 5 | Pentagon — evenly spaced around center |

**Implementation in `game.js`**:
- Define a `PRESETS` array of arrays, each containing `{x, y, mass, radius, color}` objects
- Add `setBodyCount(n)` function that replaces the `masses` array contents and respawns the rocket
- Default to 2 bodies (matches current behavior)
- Update `computeAcceleration`, `spawnRocket`, collision checks — these already iterate `masses`, so they just work as long as `masses` is updated in place
- Expose on `window` for testing

### - [x] 3. Add passive refueling
**Current behavior**: `rocket.fuel--` each tick while thrusting, no recovery.

**New behavior**:
- Add constant `REFUEL_RATE = 0.5` (half the consumption rate of 1/tick)
- Each physics tick when NOT thrusting: `rocket.fuel = Math.min(FUEL_MAX, rocket.fuel + REFUEL_RATE)`
- This means fuel recovers passively but slowly — you can't thrust forever

### - [x] 4. Add "infinite fuel" toggle option
- Add a checkbox/toggle in the controls bar: "∞ Fuel"
- When enabled, set `REFUEL_RATE` to `1.0` (matches consumption rate, so fuel never depletes while thrusting — it refuels at the same rate it's spent)
- When disabled, revert to `0.5`
- Store state in a `refuelMode` variable (`'default'` or `'infinite'`)

### - [x] 5. Initial commit
- Stage all files, commit with descriptive message

## Verification
- [x] Open `index.html` in browser
- [x] Confirm default is 2 bodies, matching current behavior
- [x] Click each body count (1–5), verify bodies appear in preset positions and rocket respawns
- [x] Thrust until fuel drops, stop thrusting, confirm fuel bar recovers slowly
- [x] Enable "∞ Fuel" toggle, thrust continuously, confirm fuel stays full
- [x] Disable toggle, confirm slow refuel resumes
