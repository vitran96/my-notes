---
para: project
status: someday
tags:
  - game-dev
  - reverse-engineering
  - android
  - zenonia
---
**Goal:** Test whether Zenonia 1–3 (Gamevil) share one engine; if they do, reverse-engineer it once and port all three versions on it.
**Done when:** the shared-engine hypothesis is confirmed or refuted from my own APK extracts.

Download sources and version tables: private Notion page "Legacy artifacts lookup" — https://app.notion.com/p/421aac09bcac81789be8f9d1caef64c9

**Hypothesis (untested):** Zenonia 1–3 share one engine. No source names the engine; the games are sequels from one studio within a few years, so it is plausible but unknown.
**Test:** extract my own APKs and compare native library names, exported symbols, asset container formats and file-tree layout.
**Shared if:** the same container format plus the same core library and symbols across versions. **Refuted if:** different formats or a rewritten core; then one port per version.

## Next actions
- [ ] Check whether my Google Play purchase history lets me re-download the games legally; if not, the project stays blocked (no APK mirror sites).
- [ ] Once I have legal APKs: get them with `adb`, unpack with Apktool/APKEditor, compare as above.

## Log
- 2026-11-23 Web research: store availability and the Zenonia 3 package id unverified; no RE project found; no evidence of a shared pipeline with Chaos Rings (different studio and publisher).

## Decisions
- 2026-11-23 The name "Zenonia engine" is a hypothesis, not a finding.
- 2026-11-23 Official or open-source sources only, no piracy links. Only clean-room notes in my own words get committed. No game files in any repo, because the games are copyrighted and the repo and vault are public.

## Compatibility
Not applicable (no server).

## Links
[[Reverse engineering]] · [[Clean-room design]] · [[Chaos Rings engine]] · [[Game development skill]]
