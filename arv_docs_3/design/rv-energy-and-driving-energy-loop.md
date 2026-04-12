# RV Energy and Driving: Energy Loop

## Scope
This document covers fuel and power state behavior and generator conversion flow.

## Resource model
- Chassis tracks `current_fuel/max_fuel` and `current_power/max_power` with signal emission on changes. (rv/chassis.gd:32, rv/chassis.gd:33, rv/chassis.gd:41, rv/chassis.gd:44)
- Mutations are clamped by `_set_fuel` and `_set_power`. (rv/chassis.gd:194, rv/chassis.gd:202)

## Energy step contract
- `step_energy_system(drive_input, braking_input, steering_input, delta) -> bool` is called from chassis physics tick before engine force application. (rv/chassis.gd:174, rv/chassis.gd:247)
- Flow:
  1. run generators (`_run_generators`) (rv/chassis.gd:178)
  2. if no drive intent: parked drain path consumes power and clamps to zero on failure (rv/chassis.gd:183, rv/chassis.gd:185)
  3. if drive intent: computes fuel needed as idle burn + drive burn * intensity (rv/chassis.gd:189)
  4. returns `false` when fuel is insufficient for this frame (rv/chassis.gd:191)
  5. on success consumes fuel and adds drive-proportional power charge (rv/chassis.gd:194, rv/chassis.gd:195)

## Generator conversion
- Chassis discovers generators by group `rv_power_generators` each step. (rv/chassis.gd:215)
- Generator must match connected RV and expose `generate_power`. (rv/chassis.gd:220, rv/chassis.gd:222)
- `Generator.generate_power(rv, delta)` computes a fuel-limited power output and applies `consume_fuel` then `add_power`. (equipment/generator.gd:10, equipment/generator.gd:33, equipment/generator.gd:39, equipment/generator.gd:40)

## Refuel exchange behavior
- `refuel_from_player` requires active item `Gasoline Can`, checks tank capacity, adds fuel, consumes active item, and gives empty can item back. (rv/chassis.gd:92, rv/chassis.gd:97, rv/chassis.gd:106)

## Source files used
- rv/chassis.gd
- equipment/generator.gd
