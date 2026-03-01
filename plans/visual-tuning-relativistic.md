# Visual Tuning & Relativistic Speed — gravityproto/game.js

## 1. Relativistic Speed Model
- [x] Add `SPEED_OF_LIGHT = 18.0`, `SPACE_DRAG = 0.0003`
- [x] Change `MAX_SPEED` from `25.0` → `20.0`
- [x] Lorentz factor thrust scaling (gravity unaffected)
- [x] Subtle space drag each frame
- [x] Hard clamp at MAX_SPEED as safety net

## 2. Nebula Visibility Boost
- [x] Brighter colors (+40-60% RGB) + 3 warm tones (crimson, teal, amber)
- [x] Blob count 1-3, radius 250-650px, opacity 0.08-0.15
- [x] Gradient midpoint 0.5/50%
- [x] Background hue shift 3x stronger, saturation 18, lightness 7

## 3. Planet Visual Toning
- [x] Outer glow alpha 0.18, smaller radius
- [x] 3D highlight +18 max 80
- [x] Gas bands 0.20-0.35, craters 0.35-0.55/0.15, volcanic 0.45-0.65, smooth 0.12-0.20, stormy 0.25-0.40
- [x] Atmosphere rim 0.10-0.25

## 4. Ship Visual Toning
- [x] Darker body gradient (#cceeee → #008877), edge #009988
- [x] Shield aura 0.06±0.03, radius 2.0
- [x] Exhaust dimmed (0.6/0.3 outer, 0.6 inner core)

## 5. Slower Background Parallax
- [x] Star parallax 0.15, nebula parallax 0.05

## 6. Verification
- [ ] Playwright screenshot verification
