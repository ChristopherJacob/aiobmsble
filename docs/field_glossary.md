# aiobmsble Field Glossary

This glossary documents the data keys used in `BMSSample` (`aiobmsble/__init__.py`).

## Core fields

| Field | Type | Unit | Meaning |
|---|---|---|---|
| `battery_charging` | `bool` | - | `True` when battery is charging. |
| `battery_mode` | `BMSMode` | - | BMS charging mode (`UNKNOWN`, `BULK`, `ABSORPTION`, `FLOAT`). |
| `battery_level` | `int \| float` | `%` | State of charge (SoC). |
| `battery_health` | `int \| float` | `%` | State of health (SoH). |
| `current` | `float` | `A` | Battery current; positive means charging. |
| `power` | `float` | `W` | Battery power; positive means charging. |
| `temperature` | `int \| float` | `degC` | Pack/average temperature in Celsius. |
| `voltage` | `float` | `V` | Pack voltage. |
| `cycle_capacity` | `int \| float` | `Wh` | Remaining energy (or cycle capacity), depending on BMS. |
| `cycles` | `int` | count | Charge/discharge cycle count. |
| `delta_voltage` | `float` | `V` | Cell voltage spread (`max(cell) - min(cell)`). |
| `problem` | `bool` | - | `True` when a problem/fault condition is detected. |
| `runtime` | `int` | `s` | Remaining runtime in seconds (typically while discharging). |

## Detailed fields

| Field | Type | Unit | Meaning |
|---|---|---|---|
| `balancer` | `bool \| int` | - | Balancer state (`False` off, `True`/`1` active, or bitmask on some BMSs). |
| `balance_current` | `float` | `A` | Balancing current. |
| `cell_count` | `int` | count | Number of series cells represented in this sample/pack. |
| `cell_voltages` | `list[float]` | `V` | Per-cell voltages. |
| `cycle_charge` | `int \| float` | `Ah` | Remaining charge/capacity in amp-hours. |
| `total_charge` | `int` | `Ah` | Total discharged amp-hours (lifetime accumulator). |
| `design_capacity` | `int` | `Ah` | Nominal/design capacity. |
| `pack_count` | `int` | count | Number of packs in a multi-pack system. |
| `temp_sensors` | `int` | count | Number of temperature sensors. |
| `temp_values` | `list[int \| float]` | `degC` | Raw temperature sensor readings in Celsius. |
| `problem_code` | `int` | - | BMS-specific fault/status code (`0` means no problem for most drivers). |

## Switch fields

| Field | Type | Unit | Meaning |
|---|---|---|---|
| `chrg_mosfet` | `bool` | - | Charge MOSFET enabled state. |
| `dischrg_mosfet` | `bool` | - | Discharge MOSFET enabled state. |
| `heater` | `bool` | - | Heater enabled/heating state. |

## Pack-level fields

| Field | Type | Unit | Meaning |
|---|---|---|---|
| `pack_voltages` | `list[float]` | `V` | Per-pack voltages in multi-pack systems. |
| `pack_currents` | `list[float]` | `A` | Per-pack currents in multi-pack systems. |
| `pack_battery_levels` | `list[int \| float]` | `%` | Per-pack state of charge. |
| `pack_battery_health` | `list[int \| float]` | `%` | Per-pack state of health. |
| `pack_cycles` | `list[int]` | count | Per-pack cycle counts. |

## Derived field notes

Many drivers provide only a subset of raw fields. `BaseBMS` may derive additional values when inputs are available, including:

- `power` from `voltage * current`
- `battery_charging` from current sign
- `temperature` from `temp_values` average
- `delta_voltage` and `cell_count` from `cell_voltages`
- `cycle_capacity` from `voltage * cycle_charge`
- `runtime` from `cycle_charge / abs(current)` while discharging

See `aiobmsble/basebms.py` (`_calculation_registry`) for exact derivation rules.
