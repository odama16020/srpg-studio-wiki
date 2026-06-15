# Assets, Units, and Data Lists

**Sources:** `api_references/asset.html` (primary spec for Unit, Class, Weapon, Item, Skill, State, Terrain, and many others), `api_references/datalist.html`, `api_references/volatile.html` (Aggregation, Parameter, SupportStatus, UnitStyle, etc.), `api_references/item.html` (item sub-infos), `Script/singleton/singleton-paramgroup.js` (ParamGroup, ParamBonus, RealBonus, UnitParameter, _configureUnitParameters), `Script/singleton/singleton-itemcontrol.js`, `Script/singleton/singleton-system.js`, and usage throughout Script + plugin_official.

## Core Asset Objects (from BaseData lists)

Most assets share: `getId()`, `getName()`, `getDescription()`, icon/face/char chip `ResourceHandle`s, `.custom()` (plain object), reference lists for skills/races/aggregations, Parameter for growth/bonus.

### Unit (most important for plugins)

Rich API:
- Basic: name (get/set), description, face handle (get/set), unit type/group, importance, isGeneratable, guest/bookmark flags.
- Class: `getClass()` / `setClass`, growth/parameter bonus.
- Combat: Lv/exp/hp (get/set), `getParamValue(index)` / set, saveMhp.
- Inventory: `getItem(index)`, `setItem`, clear, items via UnitItemControl usually.
- Skills: `getSkillReferenceList()`, new skills.
- AI / behavior: AIPattern, support data, drop trophy list.
- Position/state: `getMapX/Y`, `setMapX/Y`, direction, alive/sortie/order/reaction, wait/invisible/immortal/injury/badGuard/actionStop/syncope flags (set/is).
- Fusion/Metamorphoze: via `getUnitStyle()` → UnitStyle (source, fusion data, metamorphoze data/turn, attack data).
- Custom renderer: `getCustomCharChipHandle/Keyword/Renderer`, `setCustomRenderer`.
- Events: unit events list.
- Class change counts, class group ids.

Live units are "live" — mutating them affects the game immediately.

### Class

Equipment weapon types (ReferenceList), class type (terrain bonus flag, move type), rank, growth/parameter/skill refs, motion IDs per weapon category, max param/lv, prototype info, char chip loop, anime.

### Weapon vs Item

- Weapons: `isWeapon()` true, pow, hit, critical, weight, range (start/end), attack count, weapon level/option/category/type, effective aggregation, state invocation, weapon effect, motion collection.
- Items: `isWeapon()` false, item type (incl CUSTOM), exp on use, range value/type (or via ItemRangeControl), specific *Info getters (RecoveryInfo, DamageInfo, ClassChangeInfo, KeyInfo, StealInfo, FusionInfo, MetamorphozeInfo, ...), doping, growth, state group, custom keyword.

See `api_references/item.html` for the sub-info objects.

### Skill

Skill type/value/subvalue, invocation, range, filter flag, easy/real anime, support status, parameter bonus, data reference list (for some types), target aggregation, custom keyword, hidden, displayable.

### State

Turn, auto recovery, bad state flag/option, anime, turn change value, auto removal type, skill refs, doping (Parameter), hidden, isBadState.

### Terrain

Avoid/def/mdf, auto recovery, battle background (color keyed), move point (per move type or general), skill ref list, passable aggregation, terrain group, custom.

### Others

- Fusion/Metamorphoze: detailed conditions (src/dest unit/class/params), status calc, release types, cancel flags, item conversion.
- CompatibleData, SupportData, NewSkill.
- ShopData, OriginalData/OriginalContent (see custom params page).
- MapData: width/height, sortie max/pos, musics (map/battle variants), reinforcements, force sortie, victory/defeat conditions, shops, local switches, map type, color, custom.
- ReinforcementPos/Page, ForceSortie, MapColor, OriginalMotion, Anime (very detailed frame/sprite/motion data).

## ParamGroup & Bonus Systems (Custom Stats)

`ParamGroup._configureUnitParameters` is the hook to add custom parameters.

```js
// In a plugin (alias the configure)
var alias = ParamGroup._configureUnitParameters;
ParamGroup._configureUnitParameters = function() {
  alias.call(this);
  this.appendObject(UnitParameter.MyCustomStat);
};
```

Then define a `UnitParameter` subclass that provides name, icon, getUnitValue, etc.

Once added, `ParamBonus.getXXX(unit)`, `RealBonus.getXXX(unit)` will include it in some paths (growth, display, some calcs). Experience and doping also respect configured params.

See `Script/singleton/singleton-paramgroup.js` comments and `api_references/asset.html`.

## DataList & ReferenceList

- `DataList.getData(i)`, `getDataFromId(id)`, `getCount()`, collection variants, unit exchange.
- `ReferenceList.getTypeData(i)`, `getTypeCount()`, `getHiddenCount()`, `getObjectType()`.

Used everywhere for "the list of skills on this class", "effective weapons vs this unit", "items in stock".

## Aggregation, SupportStatus, etc.

See root-sessions-api.md and volatile.html for:
- `Aggregation.isCondition(unit)` (and from-weapon variant)
- `SupportStatus` (the 7 values)
- `Parameter` (get/set assist value by index)
- `UnitStyle` for fusion state
- `TurnState` (per-unit timed states with doping + custom)
- `Trophy`, `QuestReward`, etc.

## Best Practices

- Prefer `DataList.getDataFromId` over hardcoding indices.
- Always handle null from getters (unit may have no weapon, terrain may be null, etc.).
- Use the Control singletons (ItemControl, SkillControl, StateControl) rather than raw asset methods when possible — they layer skills/states/terrain.
- For custom stats, implement the full UnitParameter interface and test growth, doping, display, AI scoring, etc.
- OriginalData + Aggregation on assets is the cleanest way to make "editor-tweakable" conditions for your plugin logic.

## See Also

- [root-sessions-api.md](root-sessions-api.md)
- [custom-parameters-keywords-originaldata.md](custom-parameters-keywords-originaldata.md)
- [enumerated-types.md](enumerated-types.md) (ObjectType, ParamType, SkillType, ItemType, etc.)
- `api_references/asset.html` (the authoritative long reference — ~2500 lines)
- `api_references/datalist.html`, `item.html`, `volatile.html`
- `Script/singleton/singleton-paramgroup.js`
- `Script/singleton/singleton-itemcontrol.js`, `singleton-skillcontrol.js`, `singleton-statecontrol.js` (the *Control layers on top of raw assets)