---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
  - file-format
---
Small file formats of [[Beach Head 2002]] (meshes and levels have their own notes: [[Beach Head 2002 - Geometry.3D and 3DB formats]], [[Beach Head 2002 - Level scripts]]).

## Key points
- **.t24/.r24:** no header; W×H×3 bytes of RGB, top row first. Size comes from the last `WxH` in the file name, else 256×256 (quirk: `Boxes128.x128`). Black is the colour key or additive base; there is no alpha channel. All 73 files match the rule.
- **.hmap/.mmap:** 1501×1501 bytes each (2,253,001), no header, 1500×1500 cells. Heights range 8..238; lookup index = x + z·1501; world height = byte × 8.
  - The mmap has 16 values (0, then 16k−1). For values 63..239 each value is a 15-unit height band. Values 0 and 47 are overrides that don't follow height.
- **ShapeData** (hit shapes):
  - Grammar: `Object`, `Part name`, `P n` followed by n `xyz` lines, `ObjectEnd`, `End`.
  - 10 shapes in order: Tank, APC, Jet, Bomber, Cobra, CH53, C130, Parachute, BoxAir, BoxGround. 18 parts, 697 polygons.
  - The part index matters for damage (part 0 = weak spot), see [[Beach Head 2002 - Combat rules]].
- **sonidos.dat:** a u32 offset table (N = offsets[0]/4 = 5), then 24-byte entry headers (data_bytes, 8 zero bytes, rate, bits, a junk pointer) followed by raw mono PCM. Four entries are 44.1 kHz 16-bit; entry 4 is 8 kHz "bits 0", likely signed 8-bit. 187,958 bytes in total.
- **WAV/XM:** 31 WAVs, PCM 8-bit unsigned mono at 22050 Hz (6 at 11025). `Ricocht4` and `HeliAttack` are never used (slot 4 loads `Ricocht3` twice). `Menu.xm` and `preludio.xm` are XM 1.04 with 32 channels ([[XM tracker music]]).
- **UI TGA:** 175 files ([[TGA]]), type 2 uncompressed, bottom-left origin, 24/32 bpp. Each page is four tiles `01..04` (256² + 256² + two 256×128 → 512×384).
- **BH2002.ini** sections:
  - `[SCREEN]` ResX/ResY.
  - `[SETTINGS]` Resolution 0/1/2, Music, Fx, MouseMode, MouseInvert, Texturas.
  - `[NAMESCORES]`/`[SCORES]` 01..10, defaults 50, 30, 25, 20, 15, 13, 11, 9, 7, 5.
- **Installer:** `setup_eng.EXE` is a Wise self-extractor: a 15,024-byte stub, then 420 raw-deflate streams each followed by a CRC-32. 406 of them are byte-identical to the files on the disc.

## Examples
The probes that decoded these are [[NodeJS]] scripts using built-ins only; each must account for every byte ("unexplained = 0"). Method: [[Reverse engineering]]. Loaders in [[raylib]] terms: t24 → RGB image, hmap → heightfield mesh, WAV/XM natively.

## Open questions
- `.sav` save-file contents.
- Entry 4 of `sonidos.dat`: confirm signed 8-bit.
