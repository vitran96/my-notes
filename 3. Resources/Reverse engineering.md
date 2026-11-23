---
para: resource
tags:
  - reverse-engineering
---
Working out how a program or file format works from the binary and its behaviour, without the source. Applied in [[2026-11 Open Beach Head 2002]] on [[Beach Head 2002]].

## Key points

### Clean-room rule
- Two zones. Raw decompiler output, dumps and previews stay in an ignored scratch folder. Committed findings are only my own words: field tables (`offset | size | type | meaning | confidence`), prose rules, and constants with an evidence pointer (`BH2.exe @ 0x…` or a file offset).
- Confidence tags: confirmed / likely / guess / static-only.
- See [[Clean-room design]].

### Tools
- [[Ghidra]] 12.2 driven through the REA [[MCP]] server. Names were not persisted, so a names TSV is the durable record.
- `objdump -M intel` for hand-written asm.
- [[NodeJS]] built-in probe scripts that must account for every byte or token ("unexplained = 0").

### Tricks that worked
- Infer layout from file sizes (W·H·3).
- Cross-check two formats against each other (3DB vs Geometry.3D bounding boxes).
- Read the token-compare chains to recover text grammars.
- Walk deflate streams and verify their CRCs to open an installer that 7z can't.

### Runtime evidence
- The original running under [[Wine]] from [[Nix]] on [[Fedora]]. Gotcha: GL only works with the host Mesa on `LD_LIBRARY_PATH` set *inside* the nix shell.
- Per-window frame recorder at about 440 ms per frame, so effects under 1 s cannot be measured.
- Frame review by small agents was unreliable for numbers, so pixel scanning was used for the HUD bars.
- Source precedence: recording = code reading > playtest feedback > guesses.

### Verifying our port
A control channel (Unix socket, JSON lines: `step`, `state`, `input`, `screenshot`, seeded deterministic runs) with an MCP bridge, so an [[LLM]] agent can compare our port with the original.

### Process
An agent swarm (planner / elephant / goldfish), small cards, spec IDs.

## Examples
Findings produced this way: [[Beach Head 2002 - BH2.exe]], [[Beach Head 2002 - Data formats]]. Tooling: [[Jujutsu]], [[Mise-en-place]], [[CMake]], [[Ninja]], [[raylib]].

## Open questions
- Which of these tricks generalise to non-game targets?
