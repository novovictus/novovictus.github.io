# Power-Tool 18650 Rebuild Sidequest

Date started: 2026-07

## Scope

Evaluate full-cell replacement in failed 18 V-class power-tool battery packs using new matched high-drain 18650 cells, retained OEM pack electronics, and spot-welded nickel interconnects.

Initial packs:

- Ryobi ONE+ 18 V 4 Ah pack, expected 5S2P / 10-cell architecture
- DeWalt DCB207 20 V MAX 1.3 Ah pack, 5S1P / 5-cell architecture

This note is a work log, not a repair tutorial.

## Pack history

### Ryobi ONE+ 18 V 4 Ah

The pack was opened after the OEM charger would not recover it. The cell array measured approximately 9 V total.

Recovery attempts already performed:

- Multiple approximately 60-second external charge cycles
- Repeated attempts in the unmodified Ryobi charger

The pack remained at approximately 9 V and did not return to service. Further recovery attempts are not planned. The current direction is a complete 10-cell replacement while preserving the OEM board, thermistor, terminal assembly, carrier, and usable insulation.

### DeWalt DCB207

The pack was previously rebuilt successfully using recycled 18650 cells available from another project. It returned to service and survived approximately one year of actual power-tool duty cycles.

The donor cells were already known to have been abused, so long-term survival under real work cycles was not expected. The rebuild met its original goal and demonstrated that the shell, electronics, charger interface, and assembly method were viable.

The next rebuild replaces the exhausted salvage set with five new matched high-drain cells.

## Ordered materials

Liion Wholesale order S191717:

- 25 x BAK N18650COP flat-top 18650 cells
  - 2500 mAh nominal
  - 30 A listing
- 100 x flat-top positive-terminal insulators
- 20 ft x pure nickel strip
  - 8 mm wide
  - 0.15 mm thick

Order total: USD 108.06 shipped and taxed.

Planned cell allocation:

- 10 cells: Ryobi 5S2P rebuild
- 5 cells: DeWalt 5S1P rebuild
- 10 cells: reserve for another pack, destructive weld setup on non-service cells if needed, or later sidequest work

## Equipment

- Portable battery spot welder, Amazon ASIN B09V2R1TKD
- Keenstone four-bay charger, Amazon ASIN B07JD77LVD
- Digital multimeter
- IR thermometer
- Kapton tape

## Charger role and limitations

The Keenstone charger provides four independent bays, 4.2 V lithium-ion charging, selectable 500 mA or 1000 mA charge current, and per-bay indication of voltage, current, and elapsed time.

It is suitable for incoming-cell screening but is not being treated as a calibrated cell analyzer. It does not provide a controlled discharge-capacity test or a verified internal-resistance measurement.

The charger can support the following preassembly observations:

- Initial cell voltage
- Whether each cell accepts charge normally
- Approximate charge time under a common current setting
- Final indicated voltage
- Relative thermal behavior during charge
- Rested voltage after charge
- Gross self-discharge outliers

It cannot establish that every cell delivers its full rated 2500 mAh capacity.

## Incoming-cell screening plan

Number cells 01 through 25 and record:

| Cell | Incoming V | Charger bay | Charge current | Final indicated V | Charge time | Peak case temp | Rested V | Notes |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| 01 | | | 500 mA | | | | | |
| 02 | | | 500 mA | | | | | |
| 03 | | | 500 mA | | | | | |
| 04 | | | 500 mA | | | | | |
| 05 | | | 500 mA | | | | | |
| 06 | | | 500 mA | | | | | |
| 07 | | | 500 mA | | | | | |
| 08 | | | 500 mA | | | | | |
| 09 | | | 500 mA | | | | | |
| 10 | | | 500 mA | | | | | |
| 11 | | | 500 mA | | | | | |
| 12 | | | 500 mA | | | | | |
| 13 | | | 500 mA | | | | | |
| 14 | | | 500 mA | | | | | |
| 15 | | | 500 mA | | | | | |
| 16 | | | 500 mA | | | | | |
| 17 | | | 500 mA | | | | | |
| 18 | | | 500 mA | | | | | |
| 19 | | | 500 mA | | | | | |
| 20 | | | 500 mA | | | | | |
| 21 | | | 500 mA | | | | | |
| 22 | | | 500 mA | | | | | |
| 23 | | | 500 mA | | | | | |
| 24 | | | 500 mA | | | | | |
| 25 | | | 500 mA | | | | | |

Screening sequence:

1. Inspect each cell for can damage, cap deformation, wrap damage, contamination, or corrosion.
2. Measure all incoming open-circuit voltages with the same meter.
3. Charge at 500 mA for the initial qualification pass.
4. Check relative case temperature with the IR thermometer during charging.
5. Remove completed cells and allow them to rest for several hours or overnight.
6. Measure rested voltage.
7. Recheck after 24 to 48 hours when practical to identify self-discharge outliers.
8. Select the closest-behaving cells for each pack.

The objective is to reject gross outliers and assemble matched groups from one new batch. The process is not represented as calibrated capacity or impedance binning.

## Assembly assumptions

- Install a positive-terminal insulator on each new cell before laying out nickel.
- Preserve and reuse OEM carrier and insulation where intact.
- Additional fish paper and replacement wraps are not currently expected to be necessary for these builds.
- Reassess insulation after the original arrays are removed and the replacement nickel geometry is known.
- Use 8 mm x 0.15 mm pure nickel, with doubled or parallel current paths where needed to reproduce the effective OEM bus geometry.
- Establish welder settings by destructive peel testing on scrap or retired cells before welding service cells.
- Use soldering where required for board tabs, sense connections, thermistor leads, or terminal connections; avoid prolonged direct heating of cell terminals where a welded tab can be used.

## Pre-energization checks

Before connecting or charging an assembled pack:

- Verify cell orientation against the original layout.
- Verify each series-group voltage independently.
- Verify monotonic node progression from B- through all intermediate taps to B+.
- Verify total pack voltage.
- Check for unintended continuity between nickel and cell-can areas that should remain isolated.
- Inspect all welds mechanically and visually.
- Confirm thermistor placement and attachment.
- Confirm no nickel edge can contact a cell shoulder or case under compression.

## Initial success criteria

### DeWalt

- New 5S1P array is accepted by the OEM charger.
- Pack reaches normal charged voltage.
- Pack operates a representative tool without immediate voltage collapse or abnormal heating.
- Performance materially exceeds the exhausted salvage-cell rebuild.

### Ryobi

- New 5S2P array is accepted by the OEM charger.
- All five parallel groups remain closely matched during the first charge.
- Pack returns to normal tool operation without abnormal heating or early cutoff.

## Open items

- Record exact Ryobi pack model and original cell markings.
- Record individual Ryobi series-group voltages before removing the failed array, if still measurable.
- Record original bus geometry and any fuse-link construction.
- Record the exact spot-welder model markings and successful settings for 0.15 mm pure nickel.
- Determine whether the Keenstone charger bays show any consistent voltage or thermal offset.
- Add first-build results, charged voltages, tool behavior, and thermal observations.