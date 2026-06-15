# API Surface Glossary (Condensed)

**Sources:** All `api_references/*.html` (index.html for Root, session.html, asset.html, datalist.html, manager.html, generator.html, volatile.html, event.html, eventcommand.html, item.html, queryasset.html, resource.html, pattern.html, subasset.html). This is a navigation aid and quick reference — always cross-check the HTML for full signatures and return types.

## Root (index.html)

See dedicated [root-sessions-api.md](../root-sessions-api.md). Most used: getBaseData, getCurrentSession, getMetaSession, getExternalData, getEventGenerator, getObjectGenerator, createResourceHandle, isAbsoluteHit, watchTime/getElapsed*, msg/log, isTestPlay, getGraphicsManager, getMediaManager, changeScene, event command introspection, input/mouse.

## Sessions (session.html)

MetaSession, GameSession (map), RestSession, ExternalData.env — lists, terrain/unit queries, turn, simulator, gold/bonus/difficulty, global/local switches/vars, guest handling, draw helpers, map state.

## Assets (asset.html + item.html)

Unit (huge), Class, Weapon (isWeapon + combat stats), Item (type + specific *Info), Skill, State, Terrain, Fusion, Metamorphoze, Compatible/Support/NewSkill, OriginalData/Content, MapData (reinforcements, musics, victory conditions), Anime (detailed), ResourceHandle, Parameter, Aggregation, SupportStatus, UnitStyle, TurnState, Trophy, etc.

See [assets-and-datalists.md](../assets-and-datalists.md).

## Data Containers (datalist.html)

DataList (getData / getDataFromId / count / collections), ReferenceList (getTypeData / count / object type).

## Generators (generator.html)

ObjectGenerator (generate from base/bookmark/blank/reinforce, copy, delete).

EventGenerator (30+ queue methods for every command type, then execute()).

## Managers (manager.html)

SceneController (next map, battle notify, save types, complete), LoadSaveManager (files + interruption), GraphicsManager (draw/fill/text/clip/cache/composition/matrix), MediaManager (music/sound play/volume), VideoManager, MaterialManager (category/name resources), TrophyEditor/DataEditor (runtime mutation), Switch/VariableTable, UserFile, CustomizationOptions, ResourceProfiler, etc.

## Volatile / Structs (volatile.html)

ResourceHandle, Parameter, Calculation, Aggregation, SupportStatus, Trophy, Inventory, QuestReward, PrototypeInfo, UnitStyle, StateInvocation, StateGroup, TurnState, MotionIdCollection, Weapon/ScreenEffect, SaveFileInfo, ClippingArea, FormattedText, GraphicsComposition/Matrix/Canvas/Figure/Gradient/Decoration, BacklogCommand, MapSimulator (critical for ranges/AI).

See also StructureBuilder in utilities.

## Events (event.html + eventcommand.html)

Event (startEvent, is*BattleEvent, get*EventInfo, custom, executed mark).

Place/Talk/Unit/Common/Recollection event infos (positions, units, keywords, commands, conditions).

EventCommand* objects for current command introspection (getters for message text, unit, value, pos, anime, etc.).

## Resources (resource.html)

Image (draw / drawParts / stretch, set alpha/reverse/scale/degree/color/interp, animation), Media, Font, Video.

## Sub / Patterns (subasset.html, pattern.html)

Dictionaries (char/word/graphics/media), RestShop/Quest/Area, Interop* (TextUI/Screen), Layouts (shop/message/command), AIPattern (approach/move/wait/custom infos).

## Common Enums (scattered, see enumerated-types.md)

SceneType, UnitType/Group, ItemType, SkillType, WeaponOption, ParamType, DirectionType, TurnType, EventType/*EventType, Place*Type, MatchType, SelectionRangeType, Enter/MoveResult, BattleType, PatternType, Aggregation match, BadState*, MusicPlay*, Blend/Composite/Gradient, etc. + many local per system.

## Quick Lookup by Task

- **Custom item behavior**: ItemPackageControl + BaseItem* (item.html + asset.html), ItemControl (singleton)
- **AI ranges/movement**: MapSimulator + BaseCombinationCollector (volatile + map-enemyturnai)
- **Run effects from code**: EventGenerator + DynamicEvent (generator + utility-event)
- **Combat math**: Ability/Damage/Hit*Calculator (singleton-calculator)
- **Custom params/conditions**: .custom + Aggregation + OriginalContent (asset + volatile)
- **Drawing units/items/text**: *Renderer + GraphicsManager (rendercontrol + manager)
- **Custom unit visuals**: BaseCustomCharChip + setCustomRenderer (customcharchip)
- **New commands/menus**: BaseListCommand + configureCommands (base-listcommand + map-unitcommand)
- **Flows/sequences**: StraightFlow + BaseFlowEntry + push (utility-object + attack/map/item)
- **Save/config data**: env (ExternalData), global (Meta), UserFile, EnvironmentControl
- **Event commands custom**: BaseEventCommand + _configureOriginalEventCommand (eventcommand-scriptexecute)
- **Advanced gfx**: Composition/Matrix/Canvas (volatile + manager + composition examples)

## How to Use This Glossary with Sources

1. Find the concept here or in a topical wiki page.
2. Read the corresponding api_references/ HTML section for exact method signatures and "値" enum refs.
3. Grep the Script/ source for the object/method name to see real implementation and call sites.
4. Look in plugin_official/ for approved alias/registration patterns around that object.

This glossary is intentionally brief — the HTML files + Script/*.js are the ground truth. Update this page when new high-usage surface is discovered in future Script/plugin_official versions.