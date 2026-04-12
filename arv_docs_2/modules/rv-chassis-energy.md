# Module: RV Chassis and Energy

## 1. Responsibility
- Own chassis-side fuel and power state, including clamped mutation helpers and change signals.
- Apply per-frame driving-energy arbitration and generator pass integration.
- Expose durability damage and player refuel entrypoints used by other systems.

## 2. Boundaries and Dependencies
- In scope:
  - `Chassis` driving-state energy logic.
  - Chassis durability and destroyed-state gating.
  - Refuel acceptance and canister exchange logic.
- Out of scope:
  - Driver camera/occupancy UX details.
  - Generator conversion internals.
  - Fuel filler hierarchy lookup internals.
- Primary dependencies:
  - `equipment/generator.gd` instances in `rv_power_generators` group.
  - `equipment/driver_seat.gd` for driving state toggles.
  - `rv/fuel_filler.gd` for player interaction routing.

## 3. Entry Points and Public Surface
- `rv/chassis.gd`
  - `set_driving_state(state: bool) -> void`
  - `step_energy_system(drive_input, braking_input, steering_input, delta) -> bool`
  - `consume_fuel`, `add_fuel`, `consume_power`, `add_power`, `has_usable_power`
  - `refuel_from_player(player) -> void`
  - `take_damage(amount) -> void`
- `rv/fuel_filler.gd`
  - `interact(player) -> void` forwards to ancestor chassis.
- `equipment/driver_seat.gd`
  - `interact_hold(player)` and `exit_seat()` toggle chassis drive state.

## 4. Internal Structure
| Part | Role | Key Symbols | File |
|---|---|---|---|
| Chassis Runtime | Vehicle authority for motion-energy coupling | `step_energy_system`, `_physics_process`, `_run_generators` | `rv/chassis.gd` |
| Fuel Refuel Proxy | Routes interaction to chassis refuel API | `interact`, `_get_chassis` | `rv/fuel_filler.gd` |
| Power Generator Adapter | Optional fuel-to-power conversion | `generate_power` | `equipment/generator.gd` |
| Drive Seat Bridge | Player enter/exit routing for driving mode | `interact_hold`, `exit_seat` | `equipment/driver_seat.gd` |

## 5. Data Contracts
- Fuel and power are clamped to `[0, max]` and emit update signals.
- `step_energy_system(...)` returns whether driving energy requirements are satisfied for current frame intent.
- Refuel contract:
  - Requires active item name `Gasoline Can`.
  - On success, consumes active can and adds `Gasoline Can (Empty)` to RV inventory.
  - Returns early with no mutation when preconditions fail.
- Generator contract:
  - Chassis discovers generator nodes via group membership and invokes `generate_power(self, delta)`.
  - Generator-side method/field checks are duck-typed at runtime.
- Durability contract:
  - `take_damage(amount)` can force destroyed state and disable driving.

## 6. Configuration Touchpoints
- Energy and durability tuning values are exported in `rv/chassis.gd`.
- Generator conversion rates are exported in `equipment/generator.gd`.
- Runtime entry scene remains `world/test_world.tscn` from `project.godot`.

## 7. Failure Modes and Safeguards
- Insufficient fuel for intended drive frame blocks engine force path.
- Parked drain and mutation helpers clamp resources at zero rather than underflowing.
- Refuel can silently no-op for invalid player contract, wrong item, or near-full tank.
- Generator pass ignores nodes that are invalid, disconnected from this RV, or unable to satisfy expected methods.
- Destroyed chassis forces drive-off behavior to prevent continued propulsion.

## 8. Related Design Docs
- `docs/design/rv-energy-and-driving.md`

## 9. Source Files Used
- `rv/chassis.gd`
- `rv/fuel_filler.gd`
- `equipment/generator.gd`
- `equipment/driver_seat.gd`
- `project.godot`

## 10. Completeness Notes
- This module doc intentionally avoids detailed frame-by-frame walkthroughs and test matrices; those are maintained in the design doc.
- Balancing targets (for example time-to-empty/time-to-full) are not defined in code-level contracts and remain open design items.