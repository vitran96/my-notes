---
para: project
status: someday
tags:
  - game-dev
  - reverse-engineering
  - gunbound
  - private-server
---
**Goal:** Run and study an open-source GunBound (Thor's Hammer era) server and document the protocol in my own words.
**Done when:** the Java server builds with JDK 21 + Maven and starts against a local MariaDB/MySQL; the protocol is summarised in my own words.

Download sources and version tables: private Notion page "Legacy artifacts lookup" — https://app.notion.com/p/421aac09bcac81789be8f9d1caef64c9

## Next actions
- [x] Fetch kyll3r/GunBound-Java-Server and colemancda/gunbound (script `fetch-legacy.sh`).
- [ ] Review the Maven build, then build and start the broker/game/buddy servers.
- [ ] Check whether an official client is still legally obtainable (official service reportedly shut down 2026-05-29).

## Log
- 2026-11-23 Web research: Gravity Game Hub relaunched GunBound and then announced permanent shutdown; two open-source servers found.

## Decisions
- 2026-11-23 kyll3r's Java server is primary: MIT licence, names its target versions. Mjolnir (2016) and LinBound (archived 2019) are history only.
- 2026-11-23 Official or open-source sources only, no piracy links. Only clean-room notes in my own words get committed. No game files in any repo, because the games are copyrighted and the repo and vault are public.

## Compatibility
All pairings *unverified* until tested.
- kyll3r server: targets **Thor's Hammer GIS v376 / GBS v404** (stated in its readme). Ports: broker 8372, game 8360, buddy 8352 TCP / 8381 UDP. Set `server.public.ip`. No client-patch steps documented.
- colemancda server (Swift 6, macOS 13+): "Thor's Hammer", no version stated; world server port 8370, broker 8372.

## Links
[[Reverse engineering]] · [[Clean-room design]] · [[Game development skill]]
