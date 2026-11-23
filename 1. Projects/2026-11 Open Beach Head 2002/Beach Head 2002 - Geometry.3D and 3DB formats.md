---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
  - file-format
---
Mesh formats of [[Beach Head 2002]]: `Geometry.3D` (text, loaded by the game) and `.3DB` (binary authoring source, used only by the menu's vehicle viewer). Both describe the same meshes; their bounding boxes match exactly.

## Key points

### Geometry.3D
- CRLF text, 4,481,880 bytes. Starts with the word `POLY` and ends with `End`.
- Tokens are whitespace-separated words; unknown words are skipped (that is how comments work). Whole-file check: 852,232 tokens, 536 objects (slots 0..535), 26,239 polygons, 103,141 vertex lines, 73 texture lines.
- Main keywords:
  - `Object n` / `ObjectInc` / `ObjectEnd`
  - `Link`, `Instance` (317 uses), `Copy` (40 uses); a negative argument means relative to the current slot
  - `Color`, `PolyType`, `Polygon n`, `SetTexture a b c`
  - `TextureScale s` (u,v ÷ s; 255, or 127 for soldiers)
  - `TextureMap file slot bpp w h`, `ColorKeyed`
  - `Animated` (frames stacked vertically) and `Film` (frame stride `w*(h+16)*3`, so 128x29808 = 207 frames of 128x144)
  - lights `LightDistant/Point/Spot`
- PolyType → numbers per vertex line: BoundingVol 3, Smooth 4, SmoothL 6, Textured 6, SortTxtr 6, TexturedL 8 (85,775 lines), ConvertTexturedL 8, Reflected 6.
- Slot map: 8/9 sky, 10..18 terrain tiles, 37..72 tank parts, 73..116 APC parts, 117..249 aircraft, 250..293 shadows, 358..453 soldier quads, 525..530 effects, 535 crosshair.

### .3DB
- Little-endian; magic `3DBF`; u32 material count; variable-size materials (name `KENMAT_nnnn`, 18 floats, flags 0x03 textured / 0x201 glass / 0x43 rotor layers, texture layers each prefixed by f32 1.0).
- Then a recursive node tree. Root `EXPORT3DB_ARREL`. Each node: nv/nr/nf counts, bounding box, a 24-float transform whose T part is the pivot, positions, 24-byte corner records (posIndex, normal, uv), material groups, child count.
- Y up, nose toward −Z. All 9 files parse with consumed == file size.
- The layout was cracked from the data alone, with no loader code.

## Examples
The game reads `Geometry.3D` through the loader at 0x425391 ([[Beach Head 2002 - BH2.exe]]). Our [[raylib]] port loads it at runtime, never converting it. Other formats: [[Beach Head 2002 - Data formats]].

## Open questions
- Z-order and render states 0x88/0x89 of the effect quads.
