# Module Contract: Player Equipment Interactions - Placement and Crafting

## Responsibility
This component owns equipment placement state transitions and the tablet-to-crafting-station output path for RV-connected production.

## Entry points
- `Equipment.start_placement(player)` (equipment/equipment.gd:84)
- `Equipment.confirm_placement(new_global_transform, new_parent)` (equipment/equipment.gd:119)
- `Equipment.cancel_placement()` (equipment/equipment.gd:149)
- `Player._update_equipment_placement_ghost()` (player/player.gd:688)
- `TabletUI._craft_item(recipe_name)` (equipment/tablet_ui.gd:126)
- `CraftingStation.spawn_item(scene_path)` (equipment/crafting_station.gd:11)

## Placement contracts
- Placement state contract:
  - enter: `is_being_placed = true`, save original transform and parent
  - preview: ghost materials + no collision
  - confirm/cancel: restore runtime materials and finalize parent/transform choice
  (equipment/equipment.gd:84, equipment/equipment.gd:95, equipment/equipment.gd:160)
- Parent collision exception contract: confirmation adds collision exceptions with parent chain collision objects to reduce RV instability. (equipment/equipment.gd:132)
- Placement mode contract: `SURFACE` aligns equipment bottom face to hit surface; `UPRIGHT` keeps up-axis orientation while aligning against vertical surfaces. (player/player.gd:38, player/player.gd:742, player/player.gd:773)

## Crafting contracts
- Tablet RV dependency contract:
  - connected RV must provide `has_usable_power`, `has_materials`, `deduct_materials`, and fuel/power/material inventory fields/signals used by UI
  (equipment/tablet_ui.gd:99, equipment/tablet_ui.gd:121, equipment/tablet_ui.gd:124, equipment/tablet_ui.gd:153)
- Station selection contract:
  - tablet selects first node in `crafting_stations` group where `get_connected_rv() == connected_rv`
  (equipment/tablet_ui.gd:138, equipment/tablet_ui.gd:142)
- Spawn contract:
  - station must load scene, consume RV power, then spawn into world scene at marker/default location
  (equipment/crafting_station.gd:20, equipment/crafting_station.gd:25, equipment/crafting_station.gd:30)

## Failure surface
- Craft request fails when no connected RV, no usable power, insufficient materials, no station, or station not connected to same RV. (equipment/tablet_ui.gd:128, equipment/tablet_ui.gd:134, equipment/tablet_ui.gd:138, equipment/tablet_ui.gd:145)
- Spawn fails when scene load fails or RV power spend fails. (equipment/crafting_station.gd:20, equipment/crafting_station.gd:25)

## Source files used
- player/player.gd
- equipment/equipment.gd
- equipment/tablet_ui.gd
- equipment/crafting_station.gd
