# Design examples

Real boards designed, revised, or reviewed with the `power-pcb-design` skill. Each example shows what was asked, what the agent produced at each review gate, and how the result was checked.

| Example | Topology | Devices | CAD tool | Scope | Status |
|---|---|---|---|---|---|
| *No examples yet. Add the first one!* | | | | | |

## Add an example

1. Copy the [`_template`](_template/) folder and rename it, using lowercase words joined by hyphens, for example `sic-half-bridge-dut-board`.
2. Fill in the `README.md` in your new folder.
3. Put your files in the subfolders:

   | Folder | What goes in it |
   |---|---|
   | `images/` | Placement, routing, and 3D renders; photos of the built board |
   | `design/` | CAD project files, or a single zip of the project |
   | `outputs/` | Gerbers, drill files, BOM, and reports |

4. Add a row for your example to the table above.

### Uploading without Git

On GitHub, open this `examples` folder and click **Add file → Upload files**. Drag in your files, then commit. To create a new example folder from the website, use **Add file → Create new file** and type a path such as `my-board/README.md`.

### Before you upload

- **This repository is public.** Upload only designs you are allowed to share: no confidential, customer, or NDA material.
- **Keep files small.** GitHub rejects files over 100 MB and warns above 50 MB. Zip CAD projects and leave out backup folders (for example KiCad's `*-backups/`) and generated caches.
- **Use images for the overview.** Visitors browse the README first, so add a few PNG or JPG renders rather than relying on CAD files alone.
