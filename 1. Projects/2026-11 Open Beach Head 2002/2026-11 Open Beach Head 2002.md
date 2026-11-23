---
para: project
status: active
start: 2026-11-22
tags:
  - game-dev
  - reverse-engineering
  - beach-head-2002
---
**Goal:** A clean-room reimplementation of *Beach Head 2002* in [[CLang]] + [[raylib]], built from my own reverse-engineering notes, that plays like the original.
**Done when:**
- I have played our build by hand and checked it against the original, and v1.0 is tagged.
- Every S1/S2 gap in the gap list is fixed or accepted.
- A second recording of the original (night level, tank level, firing burst, Esc paths; card FX-16) has been checked against our build.
- The control-channel self-test is green.
- Builds pass on Linux, Windows and macOS.

## Next actions
- [ ] Record capture #2 of the original under [[Wine]] (night level, tank-heavy level, firing burst, Esc during the countdowns), then check gaps 22-23 against it (FX-16).
- [ ] Hand-play levels 1-10 of our build next to the original and note the differences.
- [ ] Decode the soldier frames 275..305 and the 4th soldier-blast site (the player's shell hitting the ground).
- [ ] Do audio properly: wire XM music, make loops gapless, reproduce the `Menu.xm` order-range loop 12..31.
- [ ] Run native Windows/macOS builds and CI. So far only Linux and the MinGW build under Wine were tested.
- [ ] Tag v1.0 and write the Summary section.
- [ ] Make Anki cards from [[Reverse engineering]] (`Role::GameDev::Reverse engineering`).

## Log
- 2026-11-22 Bootstrap: C + raylib + [[CMake]]/Ninja, no game assets in the repo, planner/elephant/goldfish agent swarm with spec-driven work (RE finding -> spec -> task -> code), build capped at 4 jobs. [[Ghidra]] (driven through the REA [[MCP]] server) comes from a [[Nix]] flake and tools are pinned with [[Mise-en-place]]. VCS is [[Jujutsu]].
- 2026-11-22 Inventory of the original disc: 440 files, 154 MiB. Game data lives in `BH2Game/`, `Fichas/` and `vehicles/`. The disc already has the installed layout (the installer payload is byte-identical), so no installer ever has to run.
- 2026-11-22 Phase 1 static RE done:
  - Every file format decoded (see [[Beach Head 2002 - Data formats]], [[Beach Head 2002 - Geometry.3D and 3DB formats]], [[Beach Head 2002 - Level scripts]]).
  - `BH2.exe` subsystem map written ([[Beach Head 2002 - BH2.exe]]).
  - Key learning: the "binary-looking" `Geometry.3D`, `ShapeData` and `Level_NN` are plain text read with a fscanf-style keyword reader.
- 2026-11-22 Wine inside Nix could not create a GL context (it could not see Fedora's Mesa), so behaviour specs went `static-only`. Later the same day it worked with `LD_LIBRARY_PATH` set to the host libraries inside the nix shell, and the original rendered and was captured.
- 2026-11-22 Phases 2-8 delivered once:
  - C loaders for every original format.
  - raylib renderer, terrain and sprites.
  - Headless sim; a bot clears levels 1-99.
  - Front-end menus and cross-platform toolchains.
  - Released v0.1: 7/7 CTest on Linux; the Windows cross build runs under Wine.
- 2026-11-22 Recording "rec1": 205 s of the original under Wine (levels 1-2 at 1024x768, death on level 2), sampled about every 440 ms.
- 2026-11-23 Reprioritised to fidelity first (ADR-0012). Phase F1 analysis:
  - Frame-by-frame review of rec1, plus two REA passes over `BH2.exe` and `bh2.ews`.
  - Main corrections ([[Beach Head 2002 - Combat rules]], [[Beach Head 2002 - Enemy behaviour]]): bullets cannot hurt tanks or APCs; tanks have no stop distance; APCs stop at 2800; soldiers walk at 4.0 units/tick; the muzzle flash is a 30-unit quad close to the eye; only 4 unit kinds cast shadows; Esc quits instantly; letter keys work only lowercase.
  - Gap list ranked: S1 4, S2 7, S3 12.
- 2026-11-23 Dev tools: an in-game cheat menu, plus a control channel (Unix socket, JSON lines) with a stdio MCP bridge so an AI agent can drive and inspect our game deterministically.
- 2026-11-23 Phase F2: fix cards FX-01..FX-15 implemented and checked against rec1 (12/12 checks pass). While doing FX-09 I learned that the "soldier frame table" at 0x476FA0 is really a level-load array; the frame is a per-soldier counter set by the state handlers.
- 2026-11-23 Resolution work (ADR-0011) implemented: 640x480 logical canvas for menus, native-resolution Hor+ battlefield, F11/Alt+Enter fullscreen, sizes 640x480..3840x2160.

## Decisions
- 2026-11-22 Stack is C + raylib + CMake/Ninja, because it is small, portable, and raylib loads TGA/WAV/XM natively (ADR-0001).
- 2026-11-22 No game assets or extracted data are ever committed; the game reads them at runtime from `BH_ASSET_DIR`. Reason: the game is copyrighted and the repo is public (ADR-0002, later 0017).
- 2026-11-22 Clean-room boundary with two zones. Raw decompiler output, dumps and previews stay in an ignored scratch folder. Committed specs hold only my own words: field tables, prose rules, and constants that carry an evidence pointer and a confidence tag (ADR-0005).
- 2026-11-22 Load the original formats at runtime instead of converting them. Converters exist only as RE probes. Reason: one source of truth per format, and users only extract (ADR-0006).
- 2026-11-22 RE probes are [[NodeJS]] `.mjs` scripts using built-ins only (replacing Python). Reason: Node was already pinned for REA, so nothing new gets installed (ADR-0007).
- 2026-11-22 Evidence comes from static REA/Ghidra first and running the original under Wine second. Anything not observed running is tagged `static-only` (ADR-0008).
- 2026-11-23 Fidelity before enhancements. Source precedence: recording = REA reading > my playtest requests > our guesses. A playtest request that the original contradicts gets rewritten to match the original (ADR-0012).
- 2026-11-23 Dev tools ship only in dev builds (`OBH_DEV`): a local control socket plus an MCP bridge for automated, deterministic comparisons (ADR-0013).
- 2026-11-23 Original defaults stay the defaults; changes are only opt-in comfort options (case-insensitive keys, soldier/flash scale). Esc quits at once and the pause dialog was removed (ADR-0014).

## Links
[[Beach Head 2002]] · [[Reverse engineering]] · [[Game development skill]] · [[Ghidra]] · [[Wine]] · [[raylib]] · [[CLang]] · [[Jujutsu]] · [[Fedora]]
