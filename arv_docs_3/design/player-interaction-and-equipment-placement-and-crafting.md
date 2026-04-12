# Player Interaction and Equipment: Placement and Crafting

## Scope
This document covers equipment placement lifecycle and tablet-to-station crafting behavior.

## Placement entry and preview
- Placement entry requires holding F for 2.0s while looking at an `Equipment` target, while E is not pressed and player is not already placing. (player/player_interact.gd:57, player/player_interact.gd:61)
- Entering placement sets player placement state and defaults mode to `SURFACE`. (player/player.gd:185, player/player.gd:187)
- During preview, a forward raycast controls `can_place_equipment`, orientation basis, and contact offset from equipment extents. (player/player.gd:692, player/player.gd:721, player/player.gd:757)
- R toggles placement mode between `SURFACE` and `UPRIGHT`. (player/player.gd:267)

## Equipment placement lifecycle
- `start_placement` stores original transform/parent, freezes body in kinematic mode, disables collisions, and applies ghost material recursively. (equipment/equipment.gd:84, equipment/equipment.gd:94, equipment/equipment.gd:95, equipment/equipment.gd:98)
- `confirm_placement` reparents to selected parent, preserves global transform, keeps static freeze, and adds collision exceptions up parent chain. (equipment/equipment.gd:119, equipment/equipment.gd:124, equipment/equipment.gd:130, equipment/equipment.gd:132)
- `cancel_placement` restores original parent/local transform, clears parent-chain collision exceptions, and restores original materials. (equipment/equipment.gd:149, equipment/equipment.gd:158, equipment/equipment.gd:160)

## Crafting behavior
- Tablet recipe data is script-owned and currently exposes `Gasoline Can` with material costs. (equipment/tablet_ui.gd:8)
- `on_open` resolves connected RV via parent tablet screen and rewires RV signals when connection changes. (equipment/tablet_ui.gd:30, equipment/tablet_ui.gd:33, equipment/tablet_ui.gd:99)
- Craft buttons are disabled when RV is missing, RV has no usable power, or materials are insufficient. (equipment/tablet_ui.gd:116, equipment/tablet_ui.gd:121, equipment/tablet_ui.gd:124)
- Craft execution requires a station in `crafting_stations` connected to the same RV; on success tablet deducts materials and delegates spawn to station. (equipment/tablet_ui.gd:138, equipment/tablet_ui.gd:145, equipment/tablet_ui.gd:153)

## Crafting station behavior
- `CraftingStation.spawn_item` validates RV connectivity and power, loads scene, consumes output power, and spawns into world at `SpawnMarker` when present. (equipment/crafting_station.gd:11, equipment/crafting_station.gd:16, equipment/crafting_station.gd:25, equipment/crafting_station.gd:33)
- Because crafting station extends `Equipment`, it shares the same placement lifecycle and RV connection lookup rules. (equipment/crafting_station.gd:1, equipment/equipment.gd:64)

## Source files used
- player/player.gd
- player/player_interact.gd
- equipment/equipment.gd
- equipment/tablet_ui.gd
- equipment/crafting_station.gd
