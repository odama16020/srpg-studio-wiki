# SRPG Studio Plugin Development Wiki

**LLM Wiki for writing correct, compatible plugins for SRPG Studio (English).**

This wiki sits between you (or an LLM agent) and the raw sources (`Script/`, `plugin_official/`, `api_references/`). It extracts the key extension points, patterns, data models, and gotchas so that future plugin development compounds instead of starting from zero every time.

**Primary rule:** All content is derived strictly from `Script/`, `plugin_official/`, and `api_references/`. Unofficial plugins may be studied for ideas but are never named or directly referenced here.

**Critical first read:** [es5-compatibility.md](es5-compatibility.md) — SRPG Studio uses very old JavaScript. Modern features will break.

**Last major update:** 2026-06-15 (initial creation)

## Quick Start

1. Read [getting-started.md](getting-started.md)
2. Read [es5-compatibility.md](es5-compatibility.md)
3. Read [overriding-patterns.md](overriding-patterns.md) and [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md)
4. Jump to the domain that matches your task (see categories below)
5. Cross-reference the actual source files listed at the top of each page + the api HTMLs

## Maintenance & How to Use with LLMs

- [LLM-INSTRUCTIONS.md](LLM-INSTRUCTIONS.md) — schema, ingest workflow, health checks, answer process
- [log.md](log.md) — chronological record of changes

When you (or an agent) add new official plugins or a new Script version, follow the process in LLM-INSTRUCTIONS.md, update relevant pages + index, and append to the log.

## Core Concepts (Read These Early)

- [es5-compatibility.md](es5-compatibility.md) — Non-negotiable rules + evidence from sources
- [overriding-patterns.md](overriding-patterns.md) — The alias + IIFE + configure/append + pushFlowEntry patterns (used everywhere)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) — defineObject/createObject, BaseObject cycles, StraightFlow + BaseFlowEntry, DynamicEvent
- [custom-parameters-keywords-originaldata.md](custom-parameters-keywords-originaldata.md) — .custom, keywords for CUSTOM dispatch, OriginalData/Content, Aggregation

## Calculation & Combat Math

- [calculators-damage-hit-critical.md](calculators-damage-hit-critical.md) — AbilityCalculator, DamageCalculator, HitCalculator, CriticalCalculator, support/compatible, experience, round/attack count, related is*/calculate* predicates. Most patched objects for balance.

## Items

- [item-system-custom-items.md](item-system-custom-items.md) — ItemControl family, ItemPackageControl + the 6 getCustom* hooks, the 6 BaseItem* to implement for ItemType.CUSTOM, player vs AI paths, flows for use/exp.

## AI / Enemy Behavior

- [ai-enemy-behavior.md](ai-enemy-behavior.md) — The explicit "three objects" contract (BaseCombinationCollector, BaseAIScorer, BaseAutoAction), registration via _configure*, MapSimulator, scorers, collectors, custom patterns/goals.

## Commands & Menus

- [commands-unit-map.md](commands-unit-map.md) — UnitCommand / MapCommand (and Rest/Setup/Title), configureCommands + appendObject, UnitListCommand, per-command modes and flows. Adding new player actions.

## Battle Flows & Post-Combat

- [battle-flows-and-attacks.md](battle-flows-and-attacks.md) — PreAttack, AttackFlow, virtual attack order + evaluators, Real/Easy battle UI, FlowEntry insertion points for death/exp/drop/fusion/weapon break/state removal, BaseCustomEffect.

## Events & Scripting

- [events-dynamic-script.md](events-dynamic-script.md) — ScriptCall_* hooks, DynamicEvent + EventGenerator (the main way to mutate game state from plugins), CapsuleEvent, BaseEventCommand for custom script-execute commands, event introspection.

## Data Access & Core API

- [root-sessions-api.md](root-sessions-api.md) — root global, Meta/Game/RestSession, ExternalData, BaseData lists, ObjectGenerator, EventGenerator, Graphics/Media managers, MapSimulator, common handles/aggregations.
- [assets-and-datalists.md](assets-and-datalists.md) — Unit/Class/Weapon/Item/Skill/State/Terrain (and sub-infos), ParamGroup + custom stats via _configureUnitParameters, DataList/ReferenceList, Aggregation etc.
- [utilities-and-structs.md](utilities-and-structs.md) — PosChecker, IndexArray, LayoutControl, Miscellaneous, Probability, DamageControl, StructureBuilder/ScreenBuilder (buildAttackInfo, buildItemTargetInfo, buildCombination...), Filter/Simulation controls, DataConfig/DefineControl.
- [enumerated-types.md](enumerated-types.md) — Curated important enums and CUSTOM ranges (SceneType, ItemType, SkillType, WeaponOption, ParamType, Event*, MatchType, Enter/MoveResult, etc.).

## UI, Rendering, Windows, Graphics

- [ui-rendering-windows.md](ui-rendering-windows.md) — BaseWindow/BaseScrollbar (use create*Object), all *Renderer, CustomCharChip + BaseCustomCharChip, GraphicsManager advanced (composition/matrix), message views, screens + interop, LayoutControl.
- [advanced/highlevel-and-composition.md](advanced/highlevel-and-composition.md) — Custom scenes, high-level features, Composition API (effects), WorldMatrix (transforms). Performance and hardware notes.

## References & Patterns

- [references/api-surface-glossary.md](references/api-surface-glossary.md) — Condensed navigation over all api_references/*.html with task-oriented lookup.
- [references/official-plugin-patterns.md](references/official-plugin-patterns.md) — Categorized view of plugin_official/ (ai-, calc-, custom-, battle-, skill-, terrain-, scriptexecute-, system-, window-, composition, highlevel, WorldMatrix), universal patterns observed, do's and don'ts distilled from official code/comments.

## Index of All Pages (for Agents)

Use this list + `grep` / file reads in the wiki dir when navigating.

**Foundational**
- index.md (this file)
- getting-started.md
- es5-compatibility.md
- overriding-patterns.md
- defineobject-flows-and-helpers.md
- custom-parameters-keywords-originaldata.md
- LLM-INSTRUCTIONS.md
- log.md

**Domain**
- calculators-damage-hit-critical.md
- item-system-custom-items.md
- ai-enemy-behavior.md
- commands-unit-map.md
- battle-flows-and-attacks.md
- events-dynamic-script.md
- root-sessions-api.md
- assets-and-datalists.md
- utilities-and-structs.md
- enumerated-types.md
- ui-rendering-windows.md

**References**
- references/api-surface-glossary.md
- references/official-plugin-patterns.md

**Advanced**
- advanced/highlevel-and-composition.md

## How the Wiki Was Built (Initial)

Created 2026-06-15 by systematic exploration (list_dir + heavy grep + targeted full/partial reads of key files) of:
- Script/ structure + all major singletons, command/AI/battle/item/flow files
- All ~200 files in plugin_official/ (categorized + representative full reads for patterns)
- All api_references/*.html (extracted Root, sessions, assets, generators, managers, volatile structs, events)

Focus was on extension points (alias targets, configure*/push* hooks, Base* to subclass, custom/keyword dispatch, data structs) + ES5 style + official patterns.

See log.md for the creation entry and future changes.

## Contributing / Future Expansion

- New high-ROI areas (deeper attack_*.js, more screen-specific hooks, map editor parts, full message/scroll customization) can be added as new pages or sections.
- When a new official plugin introduces a previously undocumented hook, add it to the relevant topical page + official-plugin-patterns.md + index.
- Keep examples minimal but complete (full IIFE + alias where relevant) and always ES5-clean.
- Update this index and cross-links on every addition.

The goal is a living, queryable knowledge base that lets LLMs (and humans) produce high-quality, compatible SRPG Studio plugins efficiently while respecting the constraints of the very old JavaScript environment the engine uses.

Start with the foundational pages, then the domain that matches your current task. The sources listed on each page are your next stop for precision.