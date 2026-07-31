# Fusion JLC Export

A small toolkit that turns an **Autodesk Fusion Electronics** (EAGLE) board into a
complete, ready-to-upload **JLCPCB** manufacturing set — with a single click.

One ULP produces the full JLCPCB package (Gerbers + drill + BOM + CPL, optionally an
assembly PDF). Two fab-only CAM templates cover 2- and 4-layer boards.

## What you get

Running the tool on an open board produces, in your chosen output folder:

| File | Purpose |
|------|---------|
| `<base>_gerbers.zip` | Gerbers + drill (via the selected `.cam`) — **PCB** upload |
| `<base>_jlc_bom.csv` | Bill of Materials (Comment, Designator, Footprint, LCSC Part #, …) |
| `<base>_jlc_cpl.csv` | Pick & place / centroid (CPL) |
| `<base>_assembly_top.pdf` / `_bottom.pdf` | Optional assembly drawing |
| `<base>_run.scr` | The generated script it ran (kept for inspection) |

Upload to JLCPCB: `*_gerbers.zip` for the board, `*_jlc_bom.csv` + `*_jlc_cpl.csv` for
assembly.

## Contents

- **[`fusion_jlc_export.ulp`](fusion_jlc_export.ulp)** — main tool (in the repo
  root). A dialog lets you pick the output folder, file base name, a `.cam`
  template, and export toggles.

Supporting files live in [`jlc_toolkit/`](jlc_toolkit/):

- **`jlcpcb_2_layer_fab.cam`** / **`jlcpcb_4_layer_fab.cam`** — fab-only CAM jobs
  (Gerber + drill only), safe to run from automation/script.
- **`assembly_print.scr`** — standalone assembly drawing → PDF/PNG (no CAM needed).
- **`jlc_export_all.scr`** — optional zero-click headless trigger with paths baked in.

See [`jlc_toolkit/README.md`](jlc_toolkit/README.md) for the full workflow, notes and
gotchas.

## Quick start

1. Open your board in Fusion Electronics.
2. **PCB Automation → Run ULP →** `fusion_jlc_export.ulp`.
3. In the dialog: choose output folder + base name, pick the matching CAM template
   (`2_layer` or `4_layer`), leave *Run CAM job* checked. Optionally enable the
   assembly PDF.
4. **Export.** Everything lands in the output folder — upload it to JLCPCB.

## Requirements

- Autodesk Fusion Electronics, or EAGLE 9.x (the ULP uses the EAGLE User Language).

## License

Released under the [MIT License](LICENSE).
