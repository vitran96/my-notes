---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
  - file-format
---
The 99 wave/difficulty scripts of [[Beach Head 2002]], read by the level loader at 0x429201 in [[Beach Head 2002 - BH2.exe]].

## Key points

### Format
- 99 text files `Level_%02d`, about 1.8 KB each, read as tokens.
- Case-sensitive match; unknown tokens are skipped silently. Typos in the shipped data therefore do nothing: `Nightime`, `Far`, `pasess`, `troops3`.
- A `%d` read that fails keeps the stale value. Token buffer is 64 bytes.

### Keywords
- `Ammo b s m f` (caps 500/20/20/20).
- `Aggression tank jet heliGun heliRocket` (3..9 seen).
- `Daytime` (absent = night), `Spotlight` (only counts if it comes before `Daytime`), `Crosshair`, `AltGun`/`AltGun2`, `Bombers`.
- `Object KIND` / `ObjectInc` (not bounds-checked: Level_32 overruns into other slots).
- `Delay n` in ticks (never `Delay 0`), `North/South/East/West` (codes 1–4, one per kind-group).
- C130-only keywords: `passes` (default 127), `troops1/2` (≤16), `hdrop/adrop/pdrop/mdrop` (≤4), `far`, `alternate`.

### Level numbers above 99
Levels ≥100 load `Level_(91 + n mod 10)`, so the last ten levels repeat forever.

### Threat total = the ENEMY meter
Tank 10, APC1 6, APC2/3 11, jet 10, cobra 10, CH53 6, plus troops1+troops2 per C130; bombers 0. Maximum in the shipped levels is 498; level 1 = 38 (3 APC1 + 2 cobras, ammo 400/5/10/0).

### Stats over all 99
73 day, 19 spotlight, 75 AltGun, 17 AltGun2, 30 Bombers. The difficulty curve is hand-tuned, not monotonic.

### Spawning
- Ground units appear on a ring 7500..9547 out on their edge, ±8192 along it, re-rolled until the spot is land. Jets start about 32000 out; the bomber raid brings all 9 at 65000..196000.
- Units without `Delay` are present at the start; ENEMY counts delayed units too. Killed units never respawn.

## Examples
Level 1: 3 APC1 + 2 cobras, ammo 400/5/10/0, threat total 38. What the units then do: [[Beach Head 2002 - Enemy behaviour]]. Parsing as a token stream is the same idea as the [[Read - Game Programming pattern]] data-driven designs.

## Open questions
- Effect of the C130 `alternate` flag.
