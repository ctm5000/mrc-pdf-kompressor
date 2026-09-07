# MRC PDF Kompressor — scanned documents into tiny, sharp PDFs (Windows)

A free Windows program that turns scanned pages (TIFF, JPEG, PNG) into extremely
compact PDF files using **Mixed Raster Content (MRC)** — typically **90–95 % smaller**
than the original scan, with text that stays crisp. Pattern-safe by default (no
JBIG2 symbol substitution), so the output is suitable for audit-proof archiving
(German BSI TR-RESISCAN).

**Website & download:** https://kreutzweb.de/tool-mrc-pdf-kompressor.html
([German](https://kreutzweb.de/tool-mrc-pdf-kompressor.html) · [English](https://kreutzweb.de/en/tool-mrc-pdf-kompressor.html))

![MRC PDF Kompressor — split view: original scan on the left, compressed PDF preview on the right](screenshot.png)

## Why

A colour A4 page scanned at 300 DPI is 25 MB as TIFF and still several MB as
JPEG. Archives, document management systems and mailboxes fill up fast.
Commercial MRC engines solve this, but are licensed per seat or per page
volume. This tool delivers comparable results — in our benchmarks smaller files
than leading commercial MRC products at the same legibility — and is free for
private individuals and small businesses.

## Features

- **Extreme compression**: a 5 MB scan becomes well under 100 KB in text mode
- **Pattern-safe / TR-RESISCAN**: generic JBIG2 encoding without character
  substitution — no glyph is ever replaced by a "similar" one (the classic
  JBIG2 pitfall); optional symbol mode for maximum compression when
  audit-proofness is not required
- **Colour text layers**: coloured headings, stamps, logos and highlighter
  marks are detected automatically and kept as separate, sharp 300-DPI
  layers in their original colour (up to 4 colour clusters per page)
- **Smart inpainting & background bleaching**: the background behind the text
  is reconstructed with the real paper colour, grey haze and show-through are
  removed — no halos, smaller files
- **PDF/A-1b** output for long-term archiving (ISO 19005-1)
- **Multi-page TIFF**, folders and wildcards as input, parallel page
  processing on multi-core CPUs
- **Split-view GUI** with live preview (original vs. result), synchronised
  zoom, layer view, profiles ("Text", "Graphic", …) and a generated command
  line you can paste into your scripts
- **Fully scriptable CLI**: exit codes 0/1/2, per-page progress, `--help`,
  every profile value can be overridden

## How it works

1. **Text mask** — black text is extracted at full resolution (300 DPI) and
   encoded as JBIG2 (generic region, pattern-safe; CCITT G4 as fallback)
2. **Colour text layers** — coloured text, stamps and logos are clustered by
   colour and embedded as separate stencil layers
3. **Background** — paper texture and photos are inpainted, smoothed,
   downscaled and encoded as JPEG 2000 or JPEG

The PDF stacks these layers exactly on top of each other: a fraction of the
original size, full legibility.

## Download & installation

One ZIP (~31 MB) from the website. Unzip into any folder and run
`MrcCompressor.exe` — no installer, nothing is written deep into the system.
`MrcCompressor.exe` is digitally signed (Certum certificate issued to Daniel
Kreutz); the SHA256 checksum and independent VirusTotal reports are published
on the download page.

Requirements: Windows 10/11 64-bit, .NET Framework 4.8,
[Visual C++ Redistributable x64](https://aka.ms/vc14/vc_redist.x64.exe).

## Command line

```
MrcCompressor.exe "Scan01.tif" "Archive01.pdf" --mode text --pdfa
MrcCompressor.exe C:\Scans\*.tif Batch.pdf --mode auto
MrcCompressor.exe --help
```

| Option | Meaning |
|---|---|
| `--mode auto\|text\|image\|text2\|image2` | processing strategy (auto-detects text vs. graphic pages) |
| `--pdfa` | PDF/A-1b output |
| `--threshold 0-255` | text detection sensitivity |
| `--scale 1-10` | background downscale factor (strongest size lever) |
| `--quality 0-100` | background JPEG 2000 / JPEG quality |
| `--jbig2symbols` | JBIG2 symbol matching (max. compression, **not** pattern-safe) |
| `--dpi N` | force resolution for images without DPI metadata |

Every mode starts with its profile from `mrc_profiles.xml`; explicit parameters
override the profile. Exit codes: 0 = ok, 1 = error / no page, 2 = usage error.
The full parameter reference is on the website.

## License

Licensed under the **PolyForm Small Business License 1.0.0**: free for
private individuals and for organisations with fewer than 100 people **and**
under USD 1M annual revenue — larger companies need a paid license via
[kreutzweb.de](https://kreutzweb.de). Built on excellent open-source software:
ImageMagick (Magick.NET), SkiaSharp, iTextSharp LGPL, jbig2enc + Leptonica,
potrace (GPL, sources included), PDFium, Bouncy Castle — see
THIRD-PARTY-NOTICES.txt in the package.

---

*Deutsche Fassung: [README.de.md](README.de.md)*
