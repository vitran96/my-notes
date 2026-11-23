---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
---
The game binary of [[Beach Head 2002]]: address map and findings from static analysis in [[Ghidra]]. Findings are in my own words; no decompiled code is kept here.

## Key points

### Binary facts
- PE32 i386, 251,392 bytes, built with MSVC 6 / linker 5.0, link date 2001-10, not packed.
- Image base 0x400000. Entry 0x430C30 (CRT start-up) calls WinMain at 0x40101D.
- Sections: `.text` at 0x401000, `.data` at 0x43B000; file offset of a `.data` address = VA − 0x43B000 + 0x39600.
- About 235 functions. Window class `Beachhead2`, title `3D Viewer`.

### APIs and graphics
- [[DirectX]] 7: `DirectDrawCreateEx` with IID_IDirectDraw7, then QueryInterface for Direct3D7 and a HAL device, immediate mode. 16-bit Z-buffer.
- Modes 640/800/1024 at 16 bpp. 24-bit textures are converted at load time to RGB555/565.
- DirectSound with round-robin duplicate buffers for fast-fire sounds.
- No DirectInput, no WinMM, no music. GDI `TextOutA` draws the HUD text.
- No `DispatchMessage`: input is read straight from the message queue.

### Main loop
- Runs while the run-state global 0x462230 == 3.
- Each iteration takes one key message and polls the mouse. A tick runs when more than 28 ms have passed (limiter flag), so about **34.5 Hz**.
- All motion constants are per tick, so a port must use a fixed-step sim.
- Per-tick call order: 0x403CA9 → 0x403D26 → 0x4051C8 → 0x4154B7 → 0x4021C4 → 0x402A1C → (sound) 0x424AEF → 0x4050F1.

### Key functions
| Address | Role |
|---|---|
| 0x40101D | main loop / keys |
| 0x4051C8 | AI, spawning, enemy fire, shadows (about 64 KB) |
| 0x4154B7 | player shots, projectiles, hits, kills, prizes (about 33 KB) |
| 0x4021C4 | scene setup, HUD meters, win/lose, level advance |
| 0x402A1C | HUD text |
| 0x425391 | hmap/mmap + Geometry.3D loader (about 14 KB) |
| 0x428DC3 | ShapeData parser |
| 0x429201 | level loader |
| 0x429058 / 0x4291B5 | BeachheadIn read / BeachheadOut write |
| 0x423D9D | segment-vs-polygon hit test |
| 0x424897 | bullet vs soldier |
| 0x4232FA | debris spawn |
| 0x42A62D | pools + DirectDraw init |
| 0x42AC3C | sound init |
| 0x42FEBA / 0x42FEFF | RGB555 / RGB565 converters |
| 0x42FFEC | effect-sprite instancer |
| 0x430230 | 15-bit `rand` |

151 names in total are kept in the project's names TSV. REA did not persist names, so the TSV is the record. (0x476FA0 looked like a soldier frame table but is a level-load array; the frame is a per-soldier counter set by state handlers 0x410bd0..0x413f7c.)

### Entity table
650 slots × 132 bytes at 0x44ABE0, plus parallel 124-byte state records at 0x463160. Slot ranges are fixed per type:
- TANK 0x25 (12); APC1/2/3 at 0x49/0x4D/0x51 (4 each); JET 0x75 (16); BOMBER 0x85 (9); COBRA 0x8E (12); CH53 0xB2 (8); C130 0xD2 (8).
- Shadows 0xFA..0x125; shots 0x126..0x165; infantry 0x166..0x1C5; prizes 0x1C6..0x1D5.

### Keys
- Matched on the typed character: `a b f g i m p r s 1 2 = Space Esc`, lowercase only, no Backspace.
- `=` zeroes ENEMY; `1`/`2` are the ammo/shield cheats.
- P sets bit 4 of the weapon-mode word 0x4478F0 and skips the whole tick.
- Esc sets run-state 1 at any time.

### Rendering facts
- Focal length = window width, so the horizontal FOV is about 53°.
- Muzzle flash: ring 0x477740, rows 8..15. A camera-facing 200-unit template quad (Geometry.3D object 526) is scaled to 0.15 → 30 units, placed 80 ahead (AltGun) or 220 ahead (twin guns). Screen blend, frames 0–4, lasts 5 ticks. Cannon 120, pistol 50, tank 400 units.
- Brass casings only on the AltGun paths.
- Shadows only for tank, APC, Cobra and CH53: a silhouette mesh draped at terrain height × 8 + 12, multiply blend, darkening to 0.33–0.44. Jets, bomber and C130 have none.

## Examples
Gameplay rules found in this binary: [[Beach Head 2002 - Combat rules]], [[Beach Head 2002 - Enemy behaviour]]. Formats it loads: [[Beach Head 2002 - Data formats]], [[Beach Head 2002 - Geometry.3D and 3DB formats]], [[Beach Head 2002 - Level scripts]]. Menu binary: [[Beach Head 2002 - Front-end bh2.ews]]. Tools: [[Ghidra]], [[Window OS]], [[Visual studio]] (the toolchain it was built with).

## Open questions
- Render states 0x88/0x89.
- The 28 unresolved indirect calls (COM vtables).
- Z-order of the muzzle flash against the gun model.
