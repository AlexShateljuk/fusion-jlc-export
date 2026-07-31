# JLCPCB export toolkit (Fusion Electronics)

One ULP produces the full JLCPCB set (Gerbers + drill + BOM + CPL, optionally an
assembly PDF). Two fab-only CAM templates cover 2- and 4-layer boards.

## Files

- **fusion_jlc_export.ulp** — main tool. Dialog to pick output folder, file base
  name, a `.cam` template, and toggles. Produces:
  - `<base>_jlc_bom.csv`  — BOM with Comment, Designator, Footprint, LCSC Part #, Part Number, Quantity
  - `<base>_jlc_cpl.csv`  — pick & place (centroid)
  - `<base>_gerbers.zip`  — via the chosen `.cam` (Gerbers + drill)
  - `<base>_assembly_top.pdf` / `_bottom.pdf` — optional, via the PRINT command
  - `<base>_run.scr`      — the generated script it runs (kept for inspection)
- **jlcpcb_2_layer_fab.cam** / **jlcpcb_4_layer_fab.cam** — fab-only CAM
  (Gerber + drill only). Safe to run from automation/script. Internal file names
  use the literal `PCB` instead of `%N` (which resolves to a GUID in Fusion).
- **assembly_print.scr** — standalone: assembly drawing (2 layers) → PDF/PNG,
  no CAM needed. Uses DISPLAY + PRINT / EXPORT IMAGE.
- **jlc_export_all.scr** — optional zero-click trigger: runs the ULP headless
  with paths baked in (edit the three paths inside).

## Normal workflow (one click)

1. Open your board in Fusion Electronics.
2. PCB Automation → Run ULP → `fusion_jlc_export.ulp`.
3. In the dialog:
   - Output folder + file base name (defaults to the ULP folder / project name).
   - CAM job template: pick `jlcpcb_2_layer_fab.cam` or `..._4_layer_fab.cam`.
   - Leave "Run CAM job" checked. Optionally check "Also print assembly PDF".
4. Export. Everything lands in the output folder.

Upload to JLCPCB: `*_gerbers.zip` (PCB), `*_jlc_bom.csv` + `*_jlc_cpl.csv` (assembly).

## Assembly drawing

The CAM *image* output does NOT render in automation/script mode (known Fusion
limitation), so the assembly PDF is produced with the PRINT command instead —
either from the ULP ("Also print assembly PDF") or standalone via
`assembly_print.scr`.

- Layers for a clean drawing: `20` (outline) + `21`/`22` (tPlace/bPlace) +
  `25`/`26` (tNames/bNames), optionally `51`/`52` (tDocu/bDocu).
- Do NOT display `17 18 44 45` (pads/vias/drills/holes) — those show as "+"
  crosses. Crosses that remain after that are drawn inside the footprints
  themselves (on tPlace/tDocu) and can only be removed by editing the packages.
- Print scale: blank = fit to page; `1.0` = 1:1; `2.0` = 2×, etc.

## Notes / gotchas

- Script-driven CAM writes a real `.zip` when the target ends in `.zip` and the
  `AUTOMATION` keyword is NOT used (default here). Adding `AUTOMATION` makes it
  write loose files into a folder named like the target instead — that is why a
  folder called `..._gerbers.zip` appears. Keep it without `AUTOMATION` for a zip.
- If `PRINT FILE`/`EXPORT IMAGE` pops a dialog or errors, tweak the options in
  the generated `_run.scr` or in `assembly_print.scr` — those files are plain
  text on purpose.
- To use the real project name instead of `PCB` in internal Gerber names, do a
  find/replace of `PCB` in the fab `.cam` (12 spots), or test `%PROJECTNAME`.
- Fab CAMs are script-safe. Do not add image/assembly/drawing/ODB++ back into
  them for automated runs — those must run from GUI (Process Job).
