# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project overview

`Scourge` is an Unreal Engine **5.8** project (`Scourge.uproject`, EngineAssociation `5.8`, category "Samples"). It is currently a **content-only, Blueprint/asset project**: there is no `Source/` directory, no C++ modules, no `.sln`, and no git repository. There are no build, lint, or test commands; work happens in the Unreal Editor (open `Scourge.uproject`) or through editor-side tooling (the NeoStackAI plugin is enabled).

Because assets are binary (`.uasset`/`.umap`), they cannot be meaningfully read or edited as text. Use Glob/Grep on file names and the text config files; don't try to Read `.uasset` files.

## Layout

- `Config/` — the only hand-editable text. `DefaultEngine.ini` (rendering, maps, audio), `DefaultInput.ini`, `DefaultGameplayTags.ini`, `DefaultGame.ini`, `DefaultEditor.ini`, `DefaultDeviceProfiles.ini`, `DefaultGameplayCameras.ini`.
- `Content/` — ~54 top-level folders, almost all of them **imported third-party asset packs** (character packs, animation sets, weapon/FX packs, e.g. `Lucy`, `CombatFury`, `SciFi*`, `Werewolf`, `Vefects`, `RealisticBlood`, `GunFX`), plus `Fab/` (assets downloaded from Fab), `Developers/DuaneFalconer/` (personal scratch space), and `__ExternalActors__`/`__ExternalObjects__` (World Partition / One-File-Per-Actor data; managed by the editor, never edit by hand). Pack folders are large (tens of thousands of files in total) — always narrow Glob patterns to a specific pack.
- `Extra/` — source DCC rig files (`.max`, `.mb`) for a GermanShepherd model; not used by the engine.
- `Intermediate/`, `DerivedDataCache/`, `Saved/` — generated; ignore (and exclude from any future VCS).

## Key configuration facts

- Default and editor startup map: `/Game/CombatFury/DemoCentent/Levels/DemoRoom` (note the pack's own misspelling "DemoCentent"). There is no project-specific game map yet.
- Rendering: DX12 with SM5+SM6, Lumen GI + Lumen reflections, Virtual Shadow Maps, Nanite enabled, static lighting disabled (`r.AllowStaticLighting=0`), MSAA x8 (`r.AntiAliasingMethod=4`), unlimited bone influences and 16-bit bone indices on GPU skinning. Motion blur off. Targets Desktop / Maximum graphics.
- Input uses Enhanced Input (`DefaultPlayerInputClass=EnhancedPlayerInput`); the legacy mappings in `DefaultInput.ini` are only the FlyCam debug axes.
- Gameplay tags in `DefaultGameplayTags.ini` are the engine/sample-derived set (Foley events, `Abilities.*`, etc.).
- Notable enabled plugins (in `Scourge.uproject`): Mover + NetworkPrediction, MotionWarping, PoseSearch, AnimationWarping, AnimationLocomotionLibrary, Chooser, ControlRig/LiveLink, MetaHuman suite, Chaos Cloth/Outfit, HairStrands, Reflex, GeForceNOWWrapper, plus marketplace plugins EasyPose, NeoStackAI, QOO_RootBoneAdder. Several are Win64-gated.

## Working notes

- Platform is Windows (Win64); keep paths and tooling Windows-compatible.
- Asset references are by `/Game/...` path; renaming or moving a `.uasset` on disk outside the editor breaks references — do moves/renames in the Content Browser (or via editor tooling) so redirectors are created.
- If C++ is ever added, create `Source/` with a `Scourge.Target.cs`, `ScourgeEditor.Target.cs` and a game module, add a `Modules` entry to `Scourge.uproject`, and generate project files from the `.uproject` context menu.
