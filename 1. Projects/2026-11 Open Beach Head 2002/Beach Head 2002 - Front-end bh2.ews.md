---
para: project
tags:
  - reverse-engineering
  - beach-head-2002
---
The menu/front-end binary of [[Beach Head 2002]], a different executable from the game ([[Beach Head 2002 - BH2.exe]]) with its own state machine.

## Key points
- **Binary:** PE32, about 168 KB, Exelib 0.8.0 (`Menu.cpp`). Imports DDraw, DSound, DInput, WinMM, AVIFIL32 ([[DirectX]]). Window is always 640×480 (`[SCREEN]` in the ini).
- **Menu states** (switch at 0x402050 on menu+0x82c):
  - 0 intro (blank, infogrames, digital, title, disclaimer), 1 entrance, 2 main
  - 10/11 save/load; 20/21/22 video/audio/controls; 30–32 help; 40 credits
  - 50 exit; 60/61 vehicle list/data; 80 scores; 90 final card (3 s, then exit code 0); 100 play fade (exit code 1)
- **Key addresses:**

| Address | Role |
|---|---|
| 0x401666 | click handler |
| 0x4013e0 | pull-down hit test |
| 0x4054c0 | door/frame transitions |
| 0x409390 / 0x409420 | BeachheadIn write / BeachheadOut read |
| 0x409460 | ini reader |
| 0x4099e0 | score insert |
| 0x409200 / 0x4090e0 | `.sav` load / write |
| 0x40e600 | sonidos bank loader |

- **After a game:** the entrance screen opens with the FILE pull-down showing and its text fading in over about 0.7 s.
- **Ending:** no closing video; EXIT shows the `final.tga` card for 3 s.
- **Missing art:** some art is named in the code but missing from the disc (the screenshot frames, `puertafresca*`).

## Examples
Menu music is `Menu.xm` ([[XM tracker music]]); the file formats are in [[Beach Head 2002 - Data formats]]. How it hands over to the game: see the process chain in [[Beach Head 2002]].

## Open questions
- Post-level-99 behaviour.
- Rectangles other than the main menu and pull-downs are guessed in our port.
