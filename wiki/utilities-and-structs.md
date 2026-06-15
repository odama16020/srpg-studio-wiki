# Utilities, Structs, and Common Helpers

**Key references:** Base script files such as singleton-system.js, singleton-struct.js, singleton-wrapper.js, singleton-unitlist.js, and various utility/ files. The API reference documentation (volatile.html, manager.html) and official plugins provide additional context and examples.

These are the "glue" and data construction tools used by almost every plugin.

## Position & Range (PosChecker, IndexArray, CurrentMap)

**PosChecker** (singleton-system.js):

- `getUnitFromPos(x, y)`, `getUnitFromPosEx` (ignore invisible)
- `getTerrainFromPos(x, y)`, `getTerrainFromPosEx`
- `getPlaceEventFromPos` / `FromUnit`
- `getSideDirection(x1,y1,x2,y2)`
- `getNearbyPos(unit, targetUnit)`, `getNearbyPosEx`, `getNearbyPosFromSpecificPos`
- `getMovePointFromUnit(unit, x, y)` (cost)

**IndexArray**:

- `createIndexArray(x, y, item)` — weapon or item range
- `createRangeIndexArray(x, y, rangeMetrics)`
- `getBestIndexArray(x, y, start, end)` (uses simulator internally)
- `findUnit(indexArray, targetUnit)`, `findPos`

**CurrentMap** (singleton-currentmap.js):

- `getWidth/Height/Size`, `getX(index)`, `getY(index)`, `getIndex(x,y)`, `isMapInside`
- Turn skip modes: `isCompleteSkipMode`, `setTurnSkipMode`, `isTurnSkipMode`
- `prepareMap`, cursor, etc.
- `MapLayer`, `MapView`, `MapHpControl/Decorator`, symbol/icon decorators.

## Layout & Pixel Conversion (LayoutControl)

Dozens of helpers for positioning windows, animations, units relative to camera/scroll:

- `getCenterX/Y(width/height)`
- `getMapAnimationPos(x, y, animeData)`
- `getPixelX/Y`, `getRelativeX/Y`
- `getUnitBaseX/Y`, `getUnitCenterX/Y`
- `getObjectVisibleCount`, `getNotifyY`, `getDyamicWindowY`
- Window padding, text UI, color windows, first key sprite size for cutins.

**Never hardcode** pixel positions or assume fixed resolution — use these + GraphicsFormat constants.

## Miscellaneous (Very Frequently Used)

`Miscellaneous` (singleton-system.js):

- `isPhysicsBattle(weapon)` (physics or shoot vs magic)
- `isCriticalAllowed(active, passive)`
- `isInjuryAllowed`, `isExperienceEnabled`, `isStealEnabled`, `isTradeDisabled`, `isItemAccess`, `isDurabilityChangeAllowed`, `isPlayerFreeAction`, `isStockAccess`, `isGameAcceleration`, `isSingleTextSpace`, `isUnitSrcPriority`
- `changeClass(unit, cls, isEffect)`
- `playFootstep(unit, terrain)`
- `getRandomBackgroundHandle`, `convertSpeedType`, `getFirstKeySpriteSize`, `getMotionColorIndex`, `getOriginalMotionColor`, `getMaxLv`
- `getColorWindowTextUI`, `getDyamicWindowY`
- Many internal helpers for injury, prepare scene, etc.

## Probability & Random

- `Probability.getProbability(percent)`, `getInvocationPercent`, `getInvocationProbability(skill)`, `getInvocationProbabilityFromSkill`
- `getRandomNumber()`, `getMaxPercent`
- Engine random is seeded; use root.setRandomNumberSeed for reproducibility in some cases.

## Damage / State / Weapon Effects at Runtime

- `DamageControl.setDeathState`, `setCatchState`, `setReleaseState`, `reduceHp`, `checkHp`, `isSyncope`, `isLosted`, `playDamageSound`, `getDamageAnime`
- `WeaponEffectControl`, `ScreenEffect`

## Simulation & Lists (singleton-unitlist.js)

- `PlayerList`, `EnemyList`, `AllyList`, `AllUnitList`, `TurnControl`, `FilterControl.getListArray`
- `SimulationBlockerControl`, `SimulationCostControl` (configure rules for custom move costs / blockers)
- `CostRule`, `BlockerRule`

Used heavily by AI collectors and move range previews.

## Input / Mouse / Media

- `InputControl.isSelectAction`, `isCancelAction`, `isOptionAction`, `isLeft/RightAction` etc. (InputType enum)
- `MouseControl`
- `MediaControl` for music/sound volumes and playback helpers

## StructureBuilder (Construct Data Objects for Attacks/Items/AI)

Essential when calling attack/item functions or building AI combinations from plugins.

Key builders:

- `buildAttackInfo()`
- `buildAttackParam(unit, target, startType, fusionData)`
- `buildItemTargetInfo(unit, item, targetUnit, pos, cls, targetItem, meta, isPlayerCall)`
- `buildCombination()`
- `buildVirtualAttackUnit()`
- `buildAttackEntry()`
- `buildAttackExperience()`
- `buildRangeMetrics(start, end, type)`
- `buildCostData()`
- `buildFusionParam()`
- `buildMessageParam`, `buildLevelupViewParam`, `buildMotionParam`, `buildAnimeParam`, `buildDataList` etc.

Always start with the builder then fill fields.

ScreenBuilder has `build*` for launching standard or custom screens (shop, unit menu, config, load, extra, resurrection, objective, etc.).

## Other Utilities

- `SimpleBalancer`, `GaugeBar` for HP/exp bars and animations.
- `FadeTransition`, `SystemTransition`
- `DamagePopup`, `Trophy` handling
- Anime utilities (`utility-anime.js`)
- Skill utilities, pos selector (for custom commands), panel, cursor, scroll, dictionary scrollbars, object, trophy, counter.

## Controls (Wrapper)

- `DataConfig`: many `is*` (weapon infinity, item weight display, build display, game options) and getters for factors, maxes.
- `DefineControl`: min damage/hit/crit, window sizes/padding, max lv, etc.
- `ExtraControl`: for extra screens registration
- `EnvironmentControl`, `SetupControl`, `LoadControl`, `RetryControl`, `MediaControl`

## Best Practices

- Use builders for any struct passed across systems (attack info, item target info, combinations).
- Use LayoutControl + GraphicsFormat instead of magic numbers.
- Configure simulation rules (cost/blocker) if your plugin adds new terrain/move types or flying/custom movement.
- For random, be aware of skip modes and that AI may call probability many times.
- `Miscellaneous.isPhysicsBattle` and similar are used in calculators — override or respect them when adding new weapon categories.
- Cache expensive lookups (simulator results, list arrays) when possible inside a turn/AI cycle.

## See Also

- [root-sessions-api.md](root-sessions-api.md) (many of these wrap or expose root/session)
- [ai-enemy-behavior.md](ai-enemy-behavior.md) (simulator + filter + cost usage)
- [battle-flows-and-attacks.md](battle-flows-and-attacks.md) (builders for attack)
- [item-system-custom-items.md](item-system-custom-items.md) (range + item target info)
- The base singleton-system.js (the main file with most of PosChecker/Layout/Misc)
- singleton-struct.js (builders)
- singleton-wrapper.js (DataConfig/DefineControl)
- singleton-unitlist.js
- The API reference volatile.html (structs), manager.html (some controls)

These helpers keep plugins from duplicating core logic and make them resilient to resolution/map size changes. Grep the Script/ singletons for "PosChecker" / "LayoutControl" / "Miscellaneous" / "StructureBuilder" to see idiomatic usage.