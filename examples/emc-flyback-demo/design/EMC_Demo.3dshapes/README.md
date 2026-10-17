# 3D models

The PCB links all 3D models through `${KIPRJMOD}/EMC_Demo.3dshapes/`, so this folder travels with the project.

| Models | Source |
|---|---|
| `C_*`, `R_*`, `CP_Elec_*`, `D_SMA`, `D_SMC`, `LED_*`, `PinHeader_*`, `SMDIP-4_*`, `SOIC-8_*`, `SOT-23`, `SOT-89-3` | Copied from the [KiCad 3D model library](https://gitlab.com/kicad/libraries/kicad-packages3D), licensed under CC BY-SA 4.0 with the KiCad library exception |
| `Case_UChannel_revD`, `Cover_PMMA_revD`, `Jack_4mm_RA_*`, `TestPoint_Keystone_*`, `TO-220-3_BottomMount_CaseHeatsink` | Made for this project (case, cover, and simplified sockets, test points, and bottom-mounted MOSFET) |

## Missing manufacturer models

These are not redistributed here. Download them from the manufacturer and save them in this folder under the exact file name shown, then reopen the 3D viewer (Alt+3):

| Part | File name | Download from |
|---|---|---|
| T1, Würth 750311591 | `WE_750311591_EFD25-SMD_070-4476.step` | [we-online.com](https://www.we-online.com), search for 750311591 |
| L1, Würth 744822222 | `WE-CMB_744822222.step` | [we-online.com](https://www.we-online.com), search for 744822222 |
| L2, 8 × 8 mm shielded inductor | `Bourns_SRN8040TA.step` | [bourns.com](https://www.bourns.com), SRN8040TA series |
