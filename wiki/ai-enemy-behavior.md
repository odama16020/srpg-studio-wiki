# AI and Enemy Turn Behavior

**Sources:** Explicit architecture comment and implementation in `Script/map/map-enemyturnai.js` (BaseCombinationCollector, BaseAIScorer, BaseAutoAction + many concrete collectors/scorers/actions + AutoActionBuilder, CombinationManager, CombinationBuilder), `Script/map/map-enemyturn.js` (EnemyTurn, the _configure* hooks, AutoActionBuilder integration), `Script/map/map-cource.js` (CourceBuilder), `Script/singleton/singleton-unitlist.js` (FilterControl, Simulation*Control, CostRule, BlockerRule), `Script/singleton/singleton-system.js` (PosChecker, IndexArray, AttackChecker, Miscellaneous), and patterns in `plugin_official/ai-*.js`, `plugin_official/custom-pattern.js`, `plugin_official/custom-npc.js` etc.

The comment at the top of map-enemyturnai.js is the design doc:

> // AIを作成するとは、BaseCombinationCollector、BaseAIScorer、
> // BaseAutoActionの3つを継承したオブジェクトを作成することを意味する。
> // たとえば、コマンド系スキルを使用するAIを組み込むために、
> // CombinationCollector.Skill、AIScorer.Skill、SkillAutoActionが存在している。

## The Three Pillars of Custom AI

To add new enemy behaviors you create (and register) subclasses of these three.

### 1. BaseCombinationCollector (What can this unit do, and where?)

`collectCombination(misc)` is the entry.

Core helpers you call or override:

- `_setUnitRangeCombination(misc, filter, rangeMetrics)` — finds targets in weapon/item/skill range, builds cost arrays (reachable positions), creates combinations.
- `_setPlaceRangeCombination(misc, filter, rangeMetrics)` — for place events (occupation, village, etc.).
- `_createCostArray(misc)`, `_createAndPushCombination(misc)`
- `_checkTargetScore(unit, targetUnit)`, `_arrangeFilter(unit, filter)`
- `_getTargetListArray(filterNew, misc)`

You decide the "misc" object (unit, item/skill, rangeMetrics, etc.) and push valid `combination` objects (see struct).

Subclasses in core: CombinationCollector.Weapon, .Item, .Skill, etc.

### 2. BaseAIScorer (How good is each possible action?)

`getScore(unit, combination)` returns a number (higher = better). AI picks the highest scoring valid combination.

Core scorers:
- AIScorer.Weapon, .Item, .Skill
- AIScorer.Counterattack, .Avoid
- Various first-stage / second-stage tables (AIFirstStage_*)

Many scorers look at damage, hit, recovery, terrain, support, priority flags, etc. They can return negative to disable.

`combination.plusScore` from collector is added.

### 3. BaseAutoAction (Execute the chosen action)

`setAutoActionInfo(unit, combination)`, `enterAutoAction`, `moveAutoAction`, `drawAutoAction`, `isSkipMode`, `isSkipAllowed`.

Core actions:
- WeaponAutoAction, ItemAutoAction, SkillAutoAction
- MoveAutoAction, WaitAutoAction, ScrollAutoAction

These drive the visible enemy turn: move along cource, play attack/item/skill, etc.

`AutoActionCursor` and various internal modes handle the details.

## Registration

The EnemyTurn (or equivalent) object has configuration points:

- `_configureCombinationCollector(groupArray)`
- `_configureScorerFirstStage`, `_configureScorerSecondStage`
- Similar for action builders, etc.

In your alias/override:

```js
var alias1 = EnemyTurn._configureCombinationCollector;  // name may vary slightly by version
EnemyTurn._configureCombinationCollector = function(groupArray) {
  alias1.call(this, groupArray);
  groupArray.appendObject(CombinationCollector.MyCustom);
};
```

Then also ensure the matching scorer and auto action are registered in the appropriate stages.

See `Script/map/map-enemyturn.js` around line 1113+ for the configure methods, and `plugin_official/ai-combinationselector.js`, `ai-multiplegoals.js`, `ai-scoredisabled.js` for examples of extending the tables.

## Supporting Systems

- **MapSimulator** (created via `root.getCurrentSession().createMapSimulator()`): `startSimulation(unit, movePoint)`, `startSimulationWeapon`, `startSimulationRange`, `getSimulationIndexArray`, `getSimulationMovePoint`, marks, directions, etc. Heavily used by collectors.
- **FilterControl**: `getListArray(filter, ...)` for player/enemy/ally lists with various flags.
- **Cost / Blocker rules**: `_configureCostRule`, `_configureBlockerRule` on simulation controls. Affects reachable positions.
- **CourceBuilder**: Builds the actual move path the unit will take.
- **AttackChecker**, `PosChecker.getNearbyPos`, `IndexArray` for range generation.
- `Combination` struct (from StructureBuilder): holds unit, item/skill, targetUnit, costArray, indexArray, targetPos, plusScore, isPriority, etc.

## Custom Pattern / Goal AI

Official `custom-pattern.js` shows a RANDOM movement pattern using simulator + cost arrays.

`ai-multiplegoals.js` and `ai-movepointvisible.js` demonstrate adding extra goals, visible move points for player, etc.

## Best Practices & Gotchas

- Collectors must respect aggregation (`misc.skill.getTargetAggregation().isCondition(target)` or same for item).
- Always produce valid `costArray` from reachable positions only.
- Scorers should be fast (called many times); avoid heavy simulation unless cached.
- AutoActions must handle skip modes (`this.isSkipMode()` or `CurrentMap.isCompleteSkipMode()`) so auto-battle / fast-forward doesn't hang.
- Place events with `isPriority = true` in combination get preferred targeting.
- `combination.targetPos` for non-unit goals (place events).
- Enemy decision making also goes through virtual attack simulation (see battle-flows), so calculator changes affect AI.
- For "do nothing special" just let the default Weapon/Item/Skill collectors run.

## Common Extension Goals

- New skill types that enemies can use intelligently (new collector + scorer + auto action trio).
- Smarter movement (multiple goals, terrain avoidance, formation, escort).
- Disable certain weapons/items/skills for specific units (AIDisableFlag, scorer returning negative, or custom collector filter).
- Custom place event priorities or occupation logic.
- Visible enemy move ranges / threat (player-side only, via AutoActionBuilder or drawing hooks).

## See Also

- [overriding-patterns.md](overriding-patterns.md) (the configure + append pattern)
- [defineobject-flows-and-helpers.md](defineobject-flows-and-helpers.md) (BaseAutoAction is a full enter/move/draw object)
- [commands-unit-map.md](commands-unit-map.md) (some overlap with custom player commands vs AI)
- `Script/map/map-enemyturnai.js` (the big one — read the collector/scorer/action base classes and existing concrete ones)
- `Script/map/map-enemyturn.js` (the registration and AutoActionBuilder)
- `plugin_official/ai-*.js` and `custom-pattern.js` (real official extensions)
- `api_references/volatile.html` (MapSimulator details)
- `api_references/pattern.html` (AIPattern). 

AI is one of the areas where the "three objects" contract is most explicitly documented in source comments.