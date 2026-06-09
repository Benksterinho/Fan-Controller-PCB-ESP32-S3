# InputProtection How-To (Beginner Version)

Target sheet: InputProtection

## What this sheet does
This sheet takes raw DC input from the screw terminal, protects it, and exports a cleaner rail for the rest of the board.

Think of it as:
- Input in: VIN_RAW + GND from J_PWR
- Protection: fuse + reverse-polarity diode + TVS
- Output out: VIN_PROTECTED + GND

## Parts to place
| RefDes | Part | LCSC | Why it is here |
|---|---|---|---|
| J_PWR | Conn_Screw_5.08mm_1x2 | C395939 | External power entry connector |
| F1 | Littelfuse-0466002.NRHF | C3105 | Input overcurrent protection |
| D1 | SS36 | C16015 | Reverse-polarity protection path |
| D_TVS_IN | DIODES_SMBJ26A-13-F | C135062 | Surge clamp on input rail |
| C_IN_BYP | 100nF 50V X7R 0402 | C307331 | High-frequency input bypass |
| C_IN_BULK | 100uF 50V input bulk cap | C72480 | Bulk reservoir on protected input |

## Before wiring
1. Open the root schematic.
2. If sheet InputProtection does not exist, create a hierarchical sheet named InputProtection and set its file to InputProtection.
3. Open the InputProtection subsheet.
4. Place J_PWR, F1, D1, D_TVS_IN, C_IN_BYP, and C_IN_BULK.

## Pin-level wiring (connect this to that)
Use the Wire tool and connect exactly these nets:

1. Input chain
- J_PWR VIN pin -> F1 input
- F1 output -> D1 anode
- D1 cathode -> VIN_PROTECTED net

2. Input return
- J_PWR GND pin -> GND net

3. TVS protection
- D_TVS_IN between VIN_RAW side and GND
- TVS cathode -> input side net before protection
- TVS anode -> GND

4. Bypass and bulk capacitors
- C_IN_BYP between input-side rail and GND
- C_IN_BULK between VIN_PROTECTED and GND
- C_IN_BULK positive pin -> VIN_PROTECTED
- C_IN_BULK negative pin -> GND

5. Required labels
- Add VIN_RAW label at connector side
- Add VIN_PROTECTED label after fuse/diode protection
- Add GND symbols for return net
- Add hierarchical labels VIN_RAW and VIN_PROTECTED

## Placement guidance (simple and practical)
1. Put F1 and D1 close to J_PWR to keep the unprotected path short.
2. Put D_TVS_IN physically close to the connector entry.
3. Put C_IN_BYP close to the connector and protection input side.
4. Put C_IN_BULK close to the VIN_PROTECTED output side.
5. Keep high-current input traces short and wide where practical.

## Architecture rules you must keep
1. VIN_RAW is only at the connector side, before protection.
2. VIN_PROTECTED is only after protection and feeds the power-conversion sheets.
3. Do not bypass the fuse or reverse-polarity stage.
4. Keep GND as a single shared return net.

## Root-sheet hookup check
In the root sheet, make sure:
1. VIN_RAW enters InputProtection from the external input path.
2. VIN_PROTECTED exits InputProtection to Power12V and Power3V3.
3. GND is shared consistently with the rest of the project.

## Common mistakes to avoid
1. Reversing C_IN_BULK polarity.
2. Placing VIN_RAW label after the protection chain.
3. Forgetting to connect TVS return to GND.
4. Leaving the fuse out of series with the input path.

## Final checks (2-minute checklist)
1. J_PWR input path goes through F1 and D1 before VIN_PROTECTED.
2. TVS is connected across input rail to GND.
3. C_IN_BYP and C_IN_BULK are connected to the correct rails.
4. VIN_RAW and VIN_PROTECTED labels are present and in the right locations.
5. ERC has no real wiring errors for this sheet.

## Exit criteria
- VIN_PROTECTED is generated from a protected input path.
- Input protection parts are fully wired and labeled.
- Sheet is ready to feed Power12V and Power3V3.
