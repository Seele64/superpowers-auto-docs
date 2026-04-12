# Module Contract: RV Chassis Energy - Energy Contracts

## Responsibility
This component defines fuel and power mutation contracts and generator dispatch semantics.

## Public energy API contracts
- `consume_fuel(amount) -> bool`: returns false when requested fuel exceeds available amount; no mutation for invalid spend. (rv/chassis.gd:145)
- `add_fuel(amount) -> float`: clamps and returns effective added value. (rv/chassis.gd:153)
- `consume_power(amount) -> bool`: returns false on insufficient power. (rv/chassis.gd:161)
- `add_power(amount) -> float`: clamps and returns effective added value. (rv/chassis.gd:169)
- `has_usable_power(required=0.01) -> bool`: non-negative threshold check against `current_power`. (rv/chassis.gd:174)
- `step_energy_system(...) -> bool`: authoritative per-frame energy decision gate for drive force. (rv/chassis.gd:174)

## Internal invariant contracts
- Fuel/power state changes emit `fuel_changed` and `power_changed` only on meaningful delta (`absf` threshold). (rv/chassis.gd:196, rv/chassis.gd:204)
- Fuel and power values are always clamped to `[0, max]`. (rv/chassis.gd:194, rv/chassis.gd:202)

## Generator integration contract
- Generator nodes join `rv_power_generators` group during ready. (equipment/generator.gd:8)
- Chassis invokes generator only when generator resolves to the same connected RV and exposes `generate_power`. (rv/chassis.gd:220, rv/chassis.gd:222)
- Generator requires RV to expose `consume_fuel` and `add_power` and to provide `current_power/max_power/current_fuel` fields. (equipment/generator.gd:15, equipment/generator.gd:17)

## Failure surface
- Missing generator group membership, missing generator methods, or mismatched connected RV causes silent generator skip in chassis loop.
- Fuel-insufficient drive step returns false; caller must respect this contract and block propulsion.

## Source files used
- rv/chassis.gd
- equipment/generator.gd
