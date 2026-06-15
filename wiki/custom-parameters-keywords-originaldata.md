# Custom Parameters, Keywords, and Original Data

**Sources:** Widespread usage and comments in `Script/` (singleton-paramgroup.js, singleton-itemcontrol.js, item/item-base.js, map/map-enemyturnai.js, utility/utility-event.js, constants/constants-enumeratedtype.js, singleton-system.js etc.), heavy patterns in `plugin_official/` (calc-goodweapon.js, custom-*.js for unit/class/item/skill/state/weapon, custom-drop.js, skill-*.js, terrain-*.js, eventtrigger-*.js, scriptexecute-*.js, extra_classroot.js, variable-*.js), and asset definitions in `api_references/asset.html`, `api_references/item.html`, `api_references/volatile.html`.

These mechanisms let you attach arbitrary data to editor objects and dispatch to custom code without modifying core files.

## .custom on Assets (The Workhorse)

Almost every data object and runtime unit exposes a plain JS object:

```js
// On data definitions (Class, Weapon, Item, Skill, State, Terrain, Map, etc.)
var powBonus = unit.getClass().custom.powBonus || 0;

// On live units
if (typeof unit.custom.myFlag === 'undefined') {
  unit.custom.myFlag = true;
}
var val = unit.custom.someValue;  // number, string, array, object, whatever
```

**Always guard with `typeof === 'undefined'`** before reading (prevents crashes on units that don't have the param set in the editor).

Common places:
- `unit.custom`
- `unit.getClass().custom`
- `ItemControl.getEquippedWeapon(unit).custom` (or any item)
- `skill.custom`, `state.custom`
- `terrain.custom` (from PosChecker)
- Map data, place events, etc. via `event.getPlaceEventInfo()` or map custom.

**Examples from official:**
- `plugin_official/calc-goodweapon.js`: `unit.custom.goodweapon = [{name:'剣', pow:5, hit:0, crt:0}, ...]`
- Many custom-*.js use similar for drops, anime off, parameter bonuses, state ids, etc.
- `root.getExternalData().env` for cross-save/plugin config (guarded the same way).

Persistent across sessions for env/global: `root.getMetaSession().global`, `root.getExternalData().env`.

## Keywords for Type Dispatch (CUSTOM + getCustomKeyword)

Several systems use a string keyword on the data object to select custom behavior.

### Items (ItemType.CUSTOM)

```js
// In editor: set the item's "Custom Keyword" (or via script)
function My_getCustomKeyword() {
  return 'MyPlugin_OriginalItem';
}

// Then in the package control override (see item-system-custom-items.md and overriding-patterns.md)
```

The 6 package getters receive `(item, keyword)` and you return your BaseItem* implementation if keyword matches.

### Skills (SkillType.CUSTOM)

Similar dispatch via `SkillControl.getPossessionCustomSkill(unit, keyword)` or `checkAndPushCustomSkill`, and `SkillRandomizer.isCustomSkillInvokedInternal`.

Official skill-*.js and custom-skill.js use this.

### Other Keyword Uses

- `PlaceCustomType.COMMAND` / `KEYWORD` on place events.
- Custom char chip keyword (`unit.getCustomCharChipKeyword()`).
- AIPattern custom.
- Original event commands (name match in scriptexecute).
- UnitCommand / RepeatMoveType.CUSTOM etc.

Always define a function that returns your namespaced keyword (e.g. 'Official_MyThing' or 'MyPrefix_XXX') to avoid collisions.

## Original Data / OriginalContent (Data-Driven Conditions & Values)

Editor has "Original Data" tabs (per map or global). Plugins read them for conditions without hardcoding IDs.

```js
var list = root.getBaseData().getOriginalDataList(tabIndex);
var data = list.getDataFromId(someId);
if (data !== null) {
  var content = data.getOriginalContent();
  var keyword = content.getCustomKeyword();
  var value = content.getValue();  // or getNumber()
  // content can also reference unit/class/item/skill/state/aggregation etc.
}
```

Common in:
- Event scripts (`root.getEventCommandObject().getOriginalContent()`).
- Custom place events, terrain conditions, sortie filters, reinforcement conditions.
- AI target aggregation conditions.
- Many eventtrigger-*.js and custom-placeevent.js.

See `api_references/asset.html` (OriginalData, OriginalContent) and queryasset for BaseData.

This is the preferred way to make "data only" extensions that designers can tweak in the editor.

## Aggregation (Target / Effective / Available / Passable)

Many objects carry `Aggregation`:

- `weapon.getTargetAggregation()`, `item.getTargetAggregation()`
- `skill.getTargetAggregation()`
- `terrain.getPassableAggregation()`
- Effective aggregation on weapons for特攻.
- Available aggregation for usability.

```js
var agg = item.getTargetAggregation();
if (agg !== null && agg.isCondition(targetUnit)) {
  // valid target
}
```

`Aggregation` also supports `isConditionFromWeapon(weapon)` and match types. Core for custom item/skill range + filter logic.

(See volatile.html and asset.html.)

## Other Storage Locations

- `root.getExternalData().env.yourSetting` — survives resets in some cases; good for one-time flags, config.
- `root.getMetaSession().global.xxx` — map-global, persists in save?
- Turn state custom: `TurnState` objects from DataEditor or state lists have `.custom`.
- SaveFileInfo.custom for per-save data.
- `unit.getTurnStateList()` for per-unit temporary state.

## Best Practices

1. **Namespace everything:** Prefix keywords and custom keys (`MyPlugin_Foo`, `pluginId_bar`).
2. **Guard all reads:** `typeof ... === 'undefined'`.
3. **Prefer original data + keywords** over magic numbers or hardcoded lists when possible (designer friendly).
4. **Document in the editor comment** (the big header in official plugins) exactly what custom param structure or keyword to set.
5. **For arrays in custom:** store as JSON-ish objects; iterate with classic for + length.
6. **Combine with aliasing:** read custom inside an overridden calculate/get/is* method.
7. **Clean up:** for temporary state use TurnState or unit state lists rather than polluting unit.custom forever.

## See Also

- [es5-compatibility.md](es5-compatibility.md) (the typeof guard is mandatory ES5-friendly code)
- [item-system-custom-items.md](item-system-custom-items.md) (keywords in action for items)
- [ai-enemy-behavior.md](ai-enemy-behavior.md) (aggregations in collectors)
- [calculators-damage-hit-critical.md](calculators-damage-hit-critical.md) (custom in getPower etc.)
- `plugin_official/calc-goodweapon.js`, `plugin_official/custom-*.js` (canonical examples)
- `Script/singleton/singleton-paramgroup.js` (`_configureUnitParameters` for adding new param types that then appear in .custom growth etc.)
- `api_references/asset.html`, `api_references/item.html`, `api_references/volatile.html` (full spec of custom() and OriginalContent).