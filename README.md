# Arabic Glyph Pipeline for GBA Fonts

Tooling and generated assets for rendering Arabic glyphs into a Game Boy Advance
tile-based font slot, built for a fan translation of *Golden Sun: The Lost Age*.

The pipeline converts the Rooyin pixel font (SIL OFL) into the game's native
8-bpp character format: 32 bytes per glyph, 16 px wide by 16 logical rows,
no ink on row 0 (the width byte). It then emits a codepoint map and a width
table so the game's variable-width text routine can lay Arabic out correctly.

This repository contains **only original work**: the generator scripts, the
generated bitmaps, the codepoint map, the width table, and the analysis notes.
It contains no game code, no extracted game text, and no ROM data.

## Contents

| File | Purpose |
| --- | --- |
| `build_rooyin.mjs` | Rasterises the TTF and assembles `ar_font_new.bin`, the glyph map and the width table |
| `ar_shape.mjs` | Arabic shaper: logical string to presentation-form codepoints (joining + RTL) |
| `render_rooyin.ps1` | Renders each shape 1:1 from the TTF at 10 px via GDI+ |
| `render_labels.json` | Codepoint to shape-name map (133 entries) |
| `ar_font_new.bin` | Generated font: 256 chars x 32 bytes |
| `ar_font_map.json` | Code to shape map (133 entries) |
| `ar_font_widthtbl.bin` | Width table for codepoints 0x20..0xFF |
| `ar_font_origins_v16.bin` | Earlier v16 baseline |
| `ar_font_map_origins_v16.json` | Earlier v16 map |
| `ar_font_widthtbl_origins_v16.bin` | Earlier v16 width table |
| `ar_font_final.bin` | Prior final build |
| `FONT_VALUES.*` | Per-glyph measurements (txt / json / csv) |
| `FONT_REPORT.md` | Font audit report (Arabic) |
| `POP_ANALYSIS.md` | Glyph population analysis (Arabic) |
| `FIXES_PROPOSED.md` | Proposed fixes (Arabic) |
| `rooyin_render.txt` | 1:1 render dump of every shape |
| `RooyinFree-Regular.ttf` | Source font, unmodified |
| `Rooyin-OFL.txt` | SIL Open Font License 1.1 |

## Font licence

Rooyin is Copyright 2025 The Rooyin-Perfect Project Authors
(https://Fontiran.com/Rooyin) and is licensed under the SIL Open Font License
1.1, with **Reserved Font Names "Rooyin"**. The TTF in this repository is
redistributed unmodified under that licence, and `Rooyin-OFL.txt` is included.

The Reserved Font Name means any *modified* version of the font must be renamed.
The pipeline here does not modify the font file; it rasterises it into a bitmap
in the game's own format, so no rename is required.

The generator scripts and the generated bitmaps are released under the MIT
licence (see `LICENSE`).

## Format notes

- 32 bytes per glyph, 16 px wide by 32 logical rows (16 rows x two bit-planes).
- `byte0` is the glyph width; `byte[1+2y]` is the left half (x0-7);
  `byte[2+2y]` is the right half (x8-15); rows y = 0..14.
- No ink on row 0, which carries the width byte. This is a requirement of the
  engine's text routine.
- 133 assigned glyphs: 128 Arabic shapes plus a spacer, punctuation and lam-alef
  ligatures. Codepoints run from 68 (spacer) to 218.
- The width table diverges from the stock ROM in 124 positions; every one of them
  is an assigned Arabic codepoint, so no non-Arabic code is disturbed.