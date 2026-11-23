---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
---
2002 Windows turret shooter that I reverse-engineered for [[2026-11 Open Beach Head 2002]]. Hub note: start here.

## Index
- [[Beach Head 2002 - BH2.exe]] — the game binary, address map, main loop, rendering facts.
- [[Beach Head 2002 - Front-end bh2.ews]] — the menu binary and its state machine.
- [[Beach Head 2002 - Geometry.3D and 3DB formats]] — mesh formats.
- [[Beach Head 2002 - Data formats]] — images, terrain, hit shapes, sounds, ini, installer.
- [[Beach Head 2002 - Level scripts]] — the 99 wave scripts.
- [[Beach Head 2002 - Combat rules]] — weapons, damage matrix, scoring.
- [[Beach Head 2002 - Enemy behaviour]] — per-unit state machines.
- [[Reverse engineering]] — the method and tools.

## Key points
- **Process chain.** `BH2002.exe` is a 28 KB launcher with a state variable: 0 start, 1 waiting for the menu, 2 waiting for the game, 3 error, 4 done.
  - It starts `bh2.ews` with the argument `intro`. That front-end is a PE built on the Exelweiss "Exelib 0.8.0" engine.
  - Menu exit code 0 means quit; 1 means run `BH2Game/BH2.exe`. After the game, the launcher restarts `bh2.ews` with no arguments.
- **State passing** between menu and game uses two tiny text files:
  - `BeachheadIn`: six integers = level, screen width (640/800/1024), MouseInvert, MouseMode, Fx, constant 16. The game reads the 3rd and 4th in the swapped order (an original quirk).
  - `BeachheadOut`: `<end state> <level>`, where 0 = died and 1 = Esc.
  - `BH2002.ini` holds settings and the top-10 score table.
- **Disc layout.** `BH2Game/` holds the game, models, textures, sounds and levels; `Fichas/` holds menu art, music and `sonidos.dat`; `vehicles/` holds the `.3DB` sources and skins; `SAVE/` holds 20 slots `00000001..20.sav`.
- **Inventory:** 70 `.t24`, 3 `.r24`, 175 TGA, 31 WAV, 2 XM, 99 levels, 9 `.3DB`, 1 `Geometry.3D` (4.48 MB).

## Open questions
- What is inside the `.sav` files?
- What does the original front-end do after level 99?
