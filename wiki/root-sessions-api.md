# Root, Sessions, Generators, and Core API

**Key references:** The engine's API reference documentation (index.html for Root, session.html, queryasset.html, manager.html, generator.html, volatile.html, asset.html, etc.). Usage patterns appear throughout the base scripts and official plugins.

`root` is the global entry point. Sessions provide the current game state. Generators create units and event commands programmatically.

## root (Global)

Key accessors (call these constantly):

- `root.getBaseData()` → BaseData (all master lists)
- `root.getCurrentSession()` → GameSession (on map) or RestSession
- `root.getMetaSession()` → MetaSession (gold, bonus, stock, global switches/vars, difficulty)
- `root.getExternalData()` → ExternalData (env, clear counts, save info, isUnitRegistered)
- `root.getConfigInfo()`, `root.getUserExtension()`, `root.getAnimePreference()`, `root.getStoryPreference()`, `root.getRestPreference()`
- `root.getObjectGenerator()`, `root.getEventGenerator()`
- Managers: `getGraphicsManager()`, `getMediaManager()`, `getLoadSaveManager()`, `getSceneController()`, `getDataEditor()`, `getTrophyEditor()`, etc.
- `root.createResourceHandle(isRuntime, id, colorIndex, x, y)`, `createEmptyHandle()`, `createStateInvocation(stateId, value, invocationType)`
- Utils: `isAbsoluteHit()`, `isTestPlay()`, `isHighPerfMode()`, `isEventSceneActived()`, `getLanguageCode()`, `getLocalizationFolder()`, `watchTime()` / `getElapsedTime()` / `getCycleTime()`, `getRandomNumber()`, `setRandomNumberSeed`, `msg(text)`, `log(text)`
- Drawing helpers: `drawMapAll`, `drawMapSet`, `drawUnitSet`, fade, etc.
- Screen/effect: `changeScene`, `getCurrentScene`, `getBaseScene`, `getScreenEffect`
- Event: `getEventCommandObject()`, `getEventCommandType()`, `setEventSkipMode`, `isEventSkipMode`, `endEventCommand`, `setEventExitCode`
- Input/mouse: `isInputAction`, `isInputState`, mouse pos/state
- Other: `resetGame`, `endGame`, fullscreen/soft screen, `duplicateItem`, `setMaxParameter`

See the full method list in the Root section of the API reference documentation (index.html).

## Sessions

### MetaSession (always available)

- Gold/bonus (get/set), play time, difficulty (get/set — some effects need save/reload)
- `getStockItemArray()`, `getTotalPlayerList()`
- `getGlobalSwitchTable()`, `getVariableTable(index)`
- `env` defaults, `isFirstSetup()`, `global()`

### GameSession (map scenes: BATTLESETUP, FREE, BATTLERESULT)

- Force lists: `getPlayerList()`, `getEnemyList()`, `getAllyList()`, `getGuestList()` (DataList)
- Event lists (place, auto, talk, opening, ending, communication, mapCommon)
- Current map: `getCurrentMapInfo()`, terrain `getTerrainFromPos(x, y, isLayer)`, `getUnitFromPos(x, y)` (Ex variant ignores invisible), `getPosArrayFromTerrainName`
- Cursor/scroll: get/set map cursor, scroll pixels
- Turn: count, type (PLAYER/ENEMY/ALLY), relative turn, start/end type
- `createMapSimulator()`
- Map state (MapStateType), active event unit, trophy pool + TrophyEditor
- Draw helpers: drawMap*, drawUnitSet, draw grid
- Guest join/remove

### RestSession (REST scene)

Similar lists but map/terrain/simulator mostly return null/0. Local switches.

### ExternalData

- Clear counts/points, current save info
- `env` (custom plugin params live here)
- Registration checks, active save completion flags

## BaseData & Lists

`root.getBaseData()`:

- `getPlayerList()`, `getClassList()`, `getWeaponList()`, `getItemList()`, `getSkillList()`, `getStateList()`
- Types: getWeaponType(index), ClassType, Difficulty, Npc, ClassGroup, Race, Fusion, Metamorphoze, TerrainGroup
- `getOriginalDataList(index)`, `getInteropList(index, isRuntime)`
- Resources: graphics/media/UI/font/video lists (isRuntime variants)
- Motions, effects, dictionaries, rest shops/quests/areas, bookmark units, maps, map colors, layouts (message/command/shop)
- `getPointShop()`, `getClearPointAction()`

DataList: `getData(index)`, `getDataFromId(id)`, `getCount()`, `getCollectionData`, exchange for units.

ReferenceList: for skills on classes/units, effective data, etc.

## Generators

### ObjectGenerator

- `generateUnitFromBaseUnit(baseUnit)` (for events, reinforcements — respects isGeneratable)
- `generateUnitFromBookmarkUnit(bookmarkUnit)` (requires option)
- `generateBlankPlayer(id)`
- `generateUnitFromReinforcementPage`
- `copyUnit(src, dest, flags)` (1 = dedicated weapons)
- `deleteUnit(unit)`

### EventGenerator (the big one)

Dozens of methods that queue event command data. Call several, then `execute()` to get an Event and run it (usually via DynamicEvent wrapper).

Examples: `messageShow*`, `messageTerop`, `hpRecovery`, `damageHit`, `experiencePlus`, `classChange`, `itemChange` / `unitItemChange`, `skillChange`, `stateAddition`, `goldChange`, `parameterChange`, `forceBattle`, `fusion*`, `metamorphoze*`, `mapPosOperation`, `scriptExecuteCode`, music/sound play/stop, animationPlay*, backgroundChange, screen effect/scroll, wait, unit remove/assign, chapter, location focus, bonus/trophy/durability, etc.

After queuing: `var event = generator.execute();` then check `EventCommandManager.isEventRunning(event)`.

## Important Volatile / Helper Objects

- **ResourceHandle**: graphics/sound/animation references. `isEqualHandle`, null checks, serialize/deserialize.
- **Aggregation**: `isCondition(unit)` — core for target/effective/available/passable filtering.
- **SupportStatus**: power/def/hit/avoid/crit/critAvoid/agi aggregates.
- **MapSimulator**: the workhorse for ranges, move cost, AI simulation (startSimulation*, getSimulationIndexArray/MovePoint/Direction/Mark, disable* filters).
- **Parameter**: assist/growth values.
- **StateInvocation**, **TurnState**, **UnitStyle** (fusion/metamorphoze), **Trophy**, **Inventory**, etc.
- **GraphicsComposition / Matrix / Canvas / Figure / Gradient / Decoration**: advanced drawing (see advanced page).

## Managers (Selected)

- **GraphicsManager**: fill, drawText (with color/alpha/font), clipping, cache, composition/matrix/canvas creation, map clipping/scale/rotate, async draw.
- **MediaManager**: musicPlay (handle or type), soundPlay, volumes, active music query, reset caches.
- **LoadSaveManager**: save/load files, interruption (suspend) files, copy/delete, sync.
- **SceneController**: next map, startNewMap, notifyBattleStart/End, completeGame, recollection etc.
- **TrophyEditor / DataEditor**: runtime add/delete trophies, skills, turn states (powerful for custom rewards).
- SwitchTable / VariableTable: query and set by index or id.

## Common Patterns in Plugin Code

```js
var session = root.getCurrentSession();
var simulator = session.createMapSimulator();
simulator.startSimulationRange(x, y, start, end);
var indexArray = simulator.getSimulationIndexArray();

var unit = PosChecker.getUnitFromPos(x, y);
var terrain = PosChecker.getTerrainFromPos(x, y);

var handle = root.createResourceHandle(false, 12, 0, 0, 0);  // original data id 12
```

Always null-check lists and handles.

## See Also

- The individual api_references/*.html files (especially index.html for root, session.html, generator.html, volatile.html, manager.html).
- [assets-and-datalists.md](assets-and-datalists.md)
- [utilities-and-structs.md](utilities-and-structs.md)
- The base singleton-system.js (PosChecker, IndexArray, LayoutControl, Miscellaneous, Probability — thin wrappers / re-exports of a lot of root/session power)
- singleton-currentmap.js (CurrentMap, MapView) in the base scripts
- Countless uses in every plugin_official/ file.

This surface is intentionally broad; most plugins only need a handful of accessors + generators + simulator. Read the HTML docs + grep the Script/ for real call sites when implementing.