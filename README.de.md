# MRC PDF Kompressor — gescannte Dokumente in winzige, scharfe PDFs (Windows)

Ein kostenloses Windows-Programm, das gescannte Seiten (TIFF, JPEG, PNG) mit
**Mixed Raster Content (MRC)** in extrem kompakte PDF-Dateien verwandelt —
typisch **90–95 % kleiner** als der Original-Scan, bei gestochen scharfem Text.
Standardmäßig pattern-safe (keine JBIG2-Zeichenersetzung), damit geeignet für
das ersetzende Scannen nach **BSI TR-RESISCAN**.

**Webseite & Download:** https://kreutzweb.de/tool-mrc-pdf-kompressor.html

![MRC PDF Kompressor — Split-View: links der Original-Scan, rechts die Vorschau des komprimierten PDFs](screenshot.png)

## Warum

Eine farbige A4-Seite mit 300 DPI ist als TIFF 25 MB groß und als JPEG immer
noch mehrere MB. Archive, DMS und Postfächer laufen damit schnell voll.
Kommerzielle MRC-Engines lösen das, werden aber pro Arbeitsplatz oder
Seitenvolumen lizenziert. Dieses Werkzeug liefert vergleichbare Ergebnisse —
im internen Vergleich mit 23 Musterseiten in Summe nur halb so groß wie eine
führende kommerzielle MRC-Lösung bei gleicher Lesbarkeit — und ist für
Privatpersonen und kleine Unternehmen kostenlos.

## Funktionen

- **Extreme Kompression**: aus einem 5-MB-Scan werden im Text-Modus deutlich
  unter 100 KB
- **Pattern-safe / TR-RESISCAN**: generische JBIG2-Kodierung ohne
  Zeichenersetzung — kein Zeichen wird durch ein „ähnliches" ersetzt (die
  bekannte JBIG2-Falle); optionaler Symbol-Modus für maximale Kompression,
  wenn Pattern-Sicherheit nicht gefordert ist
- **Farbtext-Ebenen**: farbige Überschriften, Stempel, Logos und Textmarker
  werden automatisch erkannt und als eigene scharfe 300-DPI-Ebenen in
  Originalfarbe erhalten (bis zu 4 Farbcluster je Seite)
- **Smart Inpainting & Background Bleaching**: der Hintergrund hinter dem Text
  wird mit der echten Papierfarbe rekonstruiert, Grauschleier und
  Durchscheinen entfernt — keine Halos, kleinere Dateien
- **PDF/A-1b** für die Langzeitarchivierung (ISO 19005-1), geprüft mit veraPDF
- **Durchsuchbar per OCR (optional)**: die integrierte Texterkennung legt eine
  unsichtbare, wortgenaue Textebene über die Seite — Volltextsuche, Markieren
  und Kopieren; läuft lokal ohne Cloud (PaddleOCR-Modelle auf ONNX Runtime),
  auch zusammen mit PDF/A, rund 6 KB je Seite
- **Mehrseitige TIFFs**, Ordner und Wildcards als Eingabe, parallele
  Seitenverarbeitung auf Mehrkern-CPUs
- **Split-View-Oberfläche** mit Echtzeit-Vorschau (Original vs. Ergebnis),
  synchronem Zoom, Ebenen-Ansicht, Profilen („Text", „Grafik", …) und einer
  generierten Kommandozeile zum Übernehmen in Skripte
- **Eigene Profile**: beliebig viele benannte Profile in der Oberfläche
  speichern und per `--profile "Name"` in Skripten aufrufen
- **Voll skriptbare CLI**: Exit-Codes 0/1/2, Fortschritt je Seite, `--help`,
  jeder Profilwert überschreibbar

## Wie es funktioniert

1. **Text-Maske** — schwarzer Text wird in voller Auflösung (300 DPI)
   extrahiert und als JBIG2 kodiert (generische Region, pattern-safe;
   CCITT G4 als Rückfallebene)
2. **Farbtext-Ebenen** — farbige Schrift, Stempel und Logos werden nach Farbe
   gruppiert und als eigene Stencil-Ebenen eingebettet
3. **Hintergrund** — Papierstruktur und Fotos werden inpainted, geglättet,
   verkleinert und als JPEG 2000 oder JPEG kodiert
4. **Textebene (optional)** — die Texterkennung legt den gelesenen Text
   unsichtbar über das Bild, damit die PDF durchsuchbar wird

Im PDF liegen diese Ebenen deckungsgleich übereinander: ein Bruchteil der
Originalgröße bei voller Lesbarkeit.

## Download & Installation

Über die Webseite als signiertes Setup (ca. 41 MB, Installation auch ohne
Administratorrechte) oder als ZIP (ca. 51 MB, davon rund 20 MB für die
Texterkennung im Ordner `ocr`). Das ZIP in einen beliebigen Ordner entpacken
und `MrcCompressor.exe` starten — nichts wird tief ins System geschrieben. Oder über den Windows-Paketmanager:

```
winget install DanielKreutz.MRCPDFKompressor
```
 `MrcCompressor.exe` ist digital signiert (Certum-Zertifikat auf
Daniel Kreutz); SHA256-Prüfsumme und unabhängige VirusTotal-Berichte stehen
auf der Download-Seite.

Voraussetzungen: Windows 10/11 64-Bit, .NET Framework 4.8,
[Visual C++ Redistributable x64](https://aka.ms/vc14/vc_redist.x64.exe) für die
Texterkennung; nur für den optionalen JBIG2-Symbol-Modus zusätzlich
[Visual C++ 2013 x86](https://aka.ms/highdpimfc2013x86enu). Das Setup installiert
fehlende Laufzeiten auf Wunsch nach.

## Kommandozeile

```
MrcCompressor.exe "Scan01.tif" "Archiv01.pdf" --mode text --pdfa --ocr
MrcCompressor.exe "Scan02.tif" "Archiv02.pdf" --profile "Mein Archivprofil"
MrcCompressor.exe C:\Scans\*.tif Stapel.pdf --mode auto
MrcCompressor.exe --help
```

| Option | Bedeutung |
|---|---|
| `--mode auto\|text\|image\|text2\|image2` | Verarbeitungsstrategie (Automatik erkennt Text- vs. Grafikseiten) |
| `--pdfa` | PDF/A-1b-Ausgabe |
| `--ocr` | unsichtbare Textebene (durchsuchbare PDF) per Texterkennung |
| `--profile "Name"` / `--profiles` | eigenes Profil aufrufen / alle Profile auflisten |
| `--threshold 0-255` | Empfindlichkeit der Texterkennung |
| `--scale 1-10` | Verkleinerungsfaktor des Hintergrunds (stärkster Größenhebel) |
| `--quality 0-100` | Qualität des Hintergrunds (JPEG 2000 / JPEG) |
| `--jbig2symbols` | JBIG2-Symbol-Matching (max. Kompression, **nicht** pattern-safe) |
| `--dpi N` | Auflösung für Bilder ohne DPI-Angabe erzwingen |

Jeder Modus startet mit seinem Profil aus `mrc_profiles.xml`; angegebene
Parameter überschreiben das Profil. Exit-Codes: 0 = ok, 1 = Fehler / keine
Seite, 2 = Bedienfehler. Die vollständige Parameter-Referenz steht auf der
Webseite.

## Lizenz

**PolyForm Small Business License 1.0.0**: kostenlos für Privatpersonen und
Organisationen mit weniger als 100 Personen **und** unter 1 Mio. USD
Jahresumsatz — größere Unternehmen benötigen eine kostenpflichtige Lizenz über
[kreutzweb.de](https://kreutzweb.de). Aufgebaut auf hervorragender
Open-Source-Software: ImageMagick (Magick.NET), SkiaSharp, iTextSharp LGPL,
jbig2enc + Leptonica, potrace (GPL, Quellcode liegt bei), PDFium, Bouncy
Castle, ONNX Runtime und PaddleOCR-Modelle — siehe THIRD-PARTY-NOTICES.txt
im Paket.
