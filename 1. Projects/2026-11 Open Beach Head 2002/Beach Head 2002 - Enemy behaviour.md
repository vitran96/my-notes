---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
---
Per-unit state machines of [[Beach Head 2002]], from the AI function at 0x4051C8 in [[Beach Head 2002 - BH2.exe]]. Damage rules are separate: [[Beach Head 2002 - Combat rules]].

## Key points

### Random numbers
C `rand` (15 bits) seeded with cursor X + Y, so runs cannot be replayed. Firing uses windows of width aggression+1, e.g. r6 = rand>>6 must land in [320, 320+aggr]. Aggression values come from [[Beach Head 2002 - Level scripts]].

### Tank
- Drives an axis-aligned line inward at speed 20 and has no stop distance.
- Each tick there is a chance of (aggr+1)/512 that it brakes, turns the turret at 1.5°/tick toward (0,860,0), runs a 75-tick fire sequence (shell at timer 15), then drives on.
- It can drive past the player.

### APC
- Lifetime 400..527 ticks. Stops when within 2800 of the centre (horizontal) or when the lifetime runs out.
- Then: brake −0.5, ramp up 2°/tick to 120° (60 ticks), drop 6 soldiers in a line along its axis, wait 90 ticks.
- APC1 then parks forever. APC2/3 drive on and fire like tanks; APC3 fires 2-rocket volleys.

### Soldiers
- Walk at 4.0 units/tick: toward a random point if farther than 2500, else toward the centre.
- At 1200 they stop advancing; under 700 they crouch.
- About 3 shoot pauses per 8 walk cycles; there are grenade poses.
- The bullet test is a narrow box (lateral <30, 0..500 along the line), not a sphere.

### Air units
- Jet: altitude 2000+0..6095, speed 160+0..31; strafing runs start when r6 is in [500, 500+j]; never drops ordnance.
- Cobra: altitude 1500+0..2047, speed 140; gun when r7 is in [40, 40+g], rockets when r7 is in the fixed window 50..53.
- CH53: ramp, release troops, then a 90-tick wait.
- C130: altitude 4000+, speed 140; does not shoot; drops paratroopers and crates over `passes` flyovers.
- Bomber: friendly, ENEMY weight 0.

## Examples
These are plain state machines, a textbook case of the State pattern in [[Read - Game Programming pattern]]; our port keeps one state enum per unit kind.

## Open questions
- Cobra turn-back rule.
- Jet climb in state 7.
- Effect of the C130 `alternate` flag.
- Soldier frames 275..305.
