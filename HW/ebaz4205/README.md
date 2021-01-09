# EBAZ4205 PCB designs and schematics

[original](./original) contains the original design (PCB only).

## Altium & KiCad import tutorial

Tools required:
- Altium 20 (21 has importer issues)
- latest KiCad nightly - [Windows builds](https://kicad-downloads.s3.cern.ch/index.html?prefix=windows/nightly/)


1\. Download original PCB file from https://github.com/Elrori/EBAZ4205/blob/master/ebit.pcb

---

2\. Import it in Altium Designer 20 or lower (v21 has a broken importer) via the DXP import wizard

2.1 Use define board shape from bounding rectangle

2.2 Other options are greyed out and turned off

2.3 20mil Internal Plane Pullback (default)

---

3\. Fix import issues in Altium

3.1.1. In PCB List (View > Panels > PCB List) locate "Pads" objects that have "Stack mode property" set to "Top-Middle-Bottom"

3.1.2. In the Properties window, change it to "Simple", keep the Shape "Rectangular"
	   (It will cause DRC issues with polygon fills which we will fix)

3.2. Polygon pour priorities seem to be screwed up and that affects KiCad (since Kicad doesn't keep around filled polygons). \
     It's possible that this is actually an issue of the original design - they poured polygons in parts, never fully.
	 \
     So even though Altium keeps previous fills, we need to to fix that in Altium (has better tools for this) so that it works in KiCad.

3.2.1. Go to Tools > Polygon Pours > Polygon Manager

3.2.2. Select all polygons (Ctrl+A) in Properties set "Hatched" to "Solid", disable "Remove Islands", keep other settings as-is

3.2.3. For every layer, Auto Generate Pour Order

3.2.4. Apply and close Polygon Manager

3.3. Default design rules mangle poly fills

3.3.1. Go to Design > Rules

3.3.2. Set "PolyonConnect" rule "Connect Style" property to "Direct Connect" \
       (This removes thermals reliefs on specific pins, but at least you don't need to dick with DRC rules too much)
	   
3.3.3. Set "Clearance_1" minimum clearance 3.2mil to pass the DRC check \
       (Or, alternatively, fix these issues, there's only 4 or so violations)

3.4. Repour all polygons via Tools > Polygon Pours > Repour All

---

4\. Save the design as ebit_ad.PcbDoc , use "PCB Binary Files" type (this is version 6 of the format, which is expected by the Kicad importer).

---

5\. Import the design in KiCad nightly (I've used 5.99.0-8236-gc3d46c1cb5, Jan 7 2021 22:02:27, CERN Windows build) 

5.1. Open pcbnew directly

5.2. File > Import > Non-KiCad board file, select ebit_ad.PcbDoc (it needs to have the PcbDoc extension!)

5.3. Fix import issues in KiCad \
     Constraints aren't migrated properly (though the values in Altium don't make much sense either) \
	 so we need to manually set it to Altium rules to get comparable results
	 
5.3.1. Go to File > Board Setup

5.3.2. Board Stackup > Physical Stackup, set Board thickness to Current thickness from stackup \
       (or actually fix / adapt it to your manufacturers capabilities)

5.3.3. Design Rules > Constraints, set to Altium rules (or manufacturer capabilities)

  - Copper > Minimum track width to 1 mils
  - Copper > Minimum annular width to 3 mils
  - Copper > Minimum via diameter to 2 mils
  - Copper > Copper edge clearance to 3.2 mils
  - Holes > Minimum through hole to 2 mils
  - Holes > Hole to hole clearance to 10 mils
  - you can ignore other settings

5.3.4. Design Rules > Violation Severity

  - set Miscellaneous > Silkscreen overlap to Ignore
  - set Miscellaneous > Silkscreen clipped by solder mask to Ignore

5.3.5. Project > Net Classes, Default+All nets, set to Altium rules (or manufacturer capabilities)

  - Clearance to 3.2 mils
  - Track Width to 10 mils
  - Via Size to 50 mils
  - Via Drill to 28 mils
  - you can ignore other settings

5.3.6. Apply and close Board Setup

5.4. Set up zone fills

5.4.1. Open Copper Zone Properties (Ctrl+Shift+Z)

5.4.2. Set Electrical Properties

  - Clearance to 3.2 mils
  - Minimum width to 3.2 mils
  - Pad connections to Solid
  - you can ignore other settings

5.4.3. Invoke "Export Settings to Other Zones" and close the Copper Zone Properties

---

6\. Due to https://gitlab.com/kicad/code/kicad/-/issues/5794 (still applicable),
   invoke zone refill in DRC checker to apply new net clearances
   \
   If you set everything as described, the DRC checker shouldn't report any errors
   and all nets should be connected.

---

7\. Save the KiCad design if you haven't already ;)
