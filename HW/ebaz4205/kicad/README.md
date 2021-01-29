# Please note

At the time of writing (Jan, 2021) you require a nightly build of KiCad to be able to view this PCB / schematic.

Once KiCad v6 lands, you will be able to use stable versions.

Nightly version was required because the significantly improved Altium PCB importer (bundled within pcbnew, not a standalone tool)
isn't available in the stable builds yet.

KiCad schematic capture (eeschema) has also switched to a new file format (s-expr),
for which it was significantly easier to write a schematic generation tool (https://github.com/tpecar/nl2sch).

## Getting KiCad nightly

If you're on Windows, CERN provides pre-built binaries (see bottom of page for latest version)

https://kicad-downloads.s3.cern.ch/index.html?prefix=windows/nightly/

For Linux it depends on the distribution - for Ubuntu/Debian, the instructions are provided on the KiCad page

https://kicad.org/download/ubuntu/#_nightly_development_builds
