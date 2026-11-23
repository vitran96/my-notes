---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
---
Weapons, damage and win/lose rules of [[Beach Head 2002]], read from `BH2.exe` ([[Beach Head 2002 - BH2.exe]], mainly 0x4154B7). The damage matrix is the most-corrected finding of the project.

## Key points

### Pools
One bullet pool at 0x462260 (80×40 bytes) is shared by player and enemies and has no owner field. The gun-shell pool 0x476E00 (6 rows) is shared by the player cannon and tank guns.

### Weapons
| Weapon | Rate / limit | Muzzle | Speed |
|---|---|---|---|
| Twin MG | 2 shots per 7 ticks (about 9.8/s) | 220 ahead, ±30 | 500 |
| AltGun | 1 per 3 ticks | 80 ahead | 500 |
| Cannon | 30-tick cooldown, cap 20 | 265 ahead | 400, gravity −0.16 |
| Pistol | clip 15 per level | 100 ahead | 400 |
| Missiles | 2 launchers, cap 20 | – | – |

- Flares: night only, light only, no decoy.
- Bomber key `B`: once per level, all 9 bombers.

### Damage matrix
- Bullets **do nothing to tanks or APCs** (just a spark). Shells and missiles kill them on any polygon.
- A tank leaves an inert braking wreck with the turret blown off, ENEMY −10. An APC is removed, ENEMY −6 only while soldiers are still aboard, plus 5 for APC2/3.
- Aircraft hit by bullets: part 0 kills; part 1 costs 2 HP, part 2 costs 1 HP. Jet HP 4, Cobra/CH53/C130 HP 8. (Part indices come from ShapeData, see [[Beach Head 2002 - Data formats]].)
- Shells and missiles kill aircraft on any part.
- Splash: soldiers within 400 of a shell or missile ground impact die.
- Enemy rockets and bombs hurt only soldiers (within 500) and the player.
- Shooting a prize crate collects it: shield +15, bullets +50, shells +4, missiles +2.

### Player damage
- Bullet within 300 of (0,800,0): −1.
- Tank shell within 250: −5, shake 2.0.
- Rocket or bomb impact within 300: −5.
- Shield is 100 at each level. Colours: green >50, amber 26..50, red <26; red also turns off the spotlight.

### Scoring and end conditions
- There is no points counter; the score is the level reached.
- ENEMY bar = 100·ENEMY/TOTAL px.
- Win: ENEMY reaches 0, then a 150-tick counter (0x447D24, shared with the prize message), then the next level runs in the same process.
- Death: the world keeps running for 150 ticks with the GAME OVER picture, then `BeachheadOut` "0 level".
- Out of ammo: the counter is cut to 90, then the shield is forced to 0.
- There are no lives.

## Examples
Level totals feeding the ENEMY bar: [[Beach Head 2002 - Level scripts]]. Who fires what: [[Beach Head 2002 - Enemy behaviour]].

## Open questions
- The 4th soldier-blast site (player's shell hitting the ground).
