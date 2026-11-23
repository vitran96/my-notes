---
para: project
status: active
start: 2026-11-23
tags:
  - game-dev
  - reverse-engineering
  - ragnarok-online
  - private-server
---
**Goal:** Run a local pre-renewal Ragnarok Online server from open-source emulators and document the client protocol and data formats in my own words.
**Done when:**
- rAthena builds in pre-renewal mode and its login, char and map servers start.
- PACKETVER and its build options are understood and written down.
- The GRF format is documented from public docs.
- Connecting a client is recorded as blocked until a legal client is found (no official pre-renewal client found).

Download sources and version tables: private Notion page "Legacy artifacts lookup" — https://app.notion.com/p/421aac09bcac81789be8f9d1caef64c9

## Next actions
- [x] Fetch rAthena and Hercules sources (script `fetch-legacy.sh` in the ai-write-notes repo; commits in its manifest).
- [ ] Review rAthena build scripts, then build with `./configure --enable-prere=yes`.
- [ ] Confirm the default PACKETVER and the override paths below on the pinned commit.
- [ ] Read the Ragnarok Research Lab GRF docs and summarise them.
- [ ] Look for an official free client distribution; record the result in Notion.

## Log
- 2026-11-23 Web research: no official pre-renewal client found; rAthena and Hercules are the open-source servers. Cloned both and read the sources.

## Decisions
- 2026-11-23 rAthena first (documented pre-renewal switch), Hercules as comparison. Both are GPL-3.0: code derived from them must stay GPL-3; my clean-room notes are not affected.
- 2026-11-23 Official or open-source sources only, no piracy links. Only clean-room notes in my own words get committed. No game files in any repo, because the games are copyrighted and the repo and vault are public.

## Compatibility
All client↔server pairings are *unverified* until I test them myself.
- rAthena: pre-renewal is `./configure --enable-prere=yes` (`configure.ac`); `src/config/renewal.hpp` gates renewal on `#ifndef PRERE`. Default PACKETVER 20211103 in `src/config/packets.hpp`; override with `--enable-packetver=YYYYMMDD` or `src/custom/defines_pre.hpp`.
- Hercules (CMake): pre-renewal is `-DENABLE_RENEWAL=OFF` (option defaults ON in `CMakeLists.txt`); `PACKETVER` is a CMake cache string; `ENABLE_PACKETVER_RE` is separate and off by default. Stable default PACKETVER 20190530 in `src/common/mmo.h`.
- Rule of thumb: the server PACKETVER must match the client executable's date. Only pairing found online: PACKETVER 20200401 with kRO 2020-05-20bRagexe, from a third-party pack (community claim, unverified).
- GRF format reference: Ragnarok Research Lab (public docs).

## Links
[[Reverse engineering]] · [[Clean-room design]] · [[Game development skill]] · [[2026-11 Open Beach Head 2002]]
