# Enumerated Types and Constants

**Key references:** The base constants-enumeratedtype.js file, scattered definitions in other base scripts, and the API reference documentation (which refers to many of these values).

Values are numbers (or bit flags). Use them for comparisons, passing to engine methods, switch statements, and aggregation filters.

## Scene & High Level

- **SceneType**: TITLE:0, GAMEOVER:1, ENDING:2, BATTLESETUP:3, FREE:4, BATTLERESULT:5, REST:6, EVENTTEST:9, EVENT:10, **CUSTOM:20** (20–49999 safe for custom scenes), RESERVED:50000.
- **ObjectType**: UNIT:0, CLASS:1, WEAPON:2, ITEM:3, SKILL:4, STATE:5, TERRAIN:101, FUSION:218, NULL:-1.

## Unit / Force

- **UnitType**: PLAYER:0, ENEMY:1, ALLY:2
- **UnitGroup**: PLAYER:0, ENEMY:1, ENEMYEVENT:2, ALLY:3, ALLYEVENT:4, REINFORCE:5, GUEST:6, GUESTEVENT:7, BOOKMARK:8
- **ImportanceType**: LEADER:0, SUBLEADER:1, MOB:2
- **AliveType**, **SortieType**, **OrderMarkType**, **ReactionType**, **UnitStatusType** etc.

## Item / Weapon

- **ItemType**: UNUSABLE:0 ... (many), **CUSTOM:100**
- **WeaponOption** (bit-ish): NONE:0, HPABSORB:1, NOGUARD:2, HPMINIMUM:3, HALVEATTACK:4, HALVEATTACKBREAK:5, SEALATTACK:6, SEALATTACKBREAK:7
- **WeaponCategoryType**: PHYSICS:0, SHOOT:1, MAGIC:2
- **RecoveryType**: SPECIFY:0, MAX:1
- **DamageType**: FIXED:0, PHYSICS:1, MAGIC:2
- **ResurrectionType**, **DurabilityType**, **StealFlag**, **QuickValue** etc.
- **SelectionRangeType**: SELFONLY:0, MULTI:1, ALL:2

## Skill

- **SkillType** (major): FASTATTACK:0, CONTINUOUSATTACK:1, ..., SUPPORT:33, PARAMBONUS:34, STEAL:40, ..., REPEATMOVE:50, REACTION:51, **CUSTOM:100**
- **InvocationType**: (ParamType +) LV:11, ABSOLUTE:12, HPDOWN:13
- **InvalidFlag**, **BattleRestrictionValue**, **SurvivalValue**, **TrueHitValue**

## Param

- **ParamType**: MHP:0, POW:1, MAG:2, SKI:3, SPD:4, LUK:5, DEF:6, MDF:7, MOV:8, WLV:9, BLD:10, COUNT:11
- **IncreaseType**: INCREASE:0, DECREASE:1, ALLRELEASE:2, ASSIGNMENT:3
- **DopingType**, **ExperiencePlusType**

## AI / Move / Pattern

- **PatternType**: APPROACH:0, WAIT:1, MOVE:2, **CUSTOM:100**, ESTIMATE:200
- **MoveAIType**: MOVEONLY:0, BLOCK:1, APPROACH:2
- **MoveGoalType**: POS:0, UNIT:1
- **LockonType**, **AIDisableFlag** (bit: WEAPON 0x01, ITEM 0x02, SKILL 0x04)
- **DirectionType**: LEFT:0, TOP:1, RIGHT:2, BOTTOM:3, NULL:4

## Event

- **EventType**: PLACE:0, AUTO:1, TALK:2, UNIT:3, OPENING:4, ENDING:5, COMMUNICATION:6, RECOLLECTION:7, DYNAMIC:8, MAPCOMMON:9
- **UnitEventType**: DEAD:0, INJURY:1, ACTIVETURN:2, BATTLE:3, COMMAND:4
- **PlaceEventType**: VILLAGE:0, TREASURE:1, OCCUPATION:2, SHOP:3, GATE:4, WAIT:5, INFORMATION:6, **CUSTOM:100**
- **PlaceCustomType**: COMMAND:0, KEYWORD:1
- **CommunicationEventType**, **MessagePos**: TOP:0, CENTER:1, BOTTOM:2, NONE:-1
- **EventExecutedType**, **EventResult**

## Battle / Attack

- **BattleType**: REAL:0, EASY:1 (+ force variants)
- **AttackTemplateType**: FIGHTER:0, ARCHER:1, MAGE:2, FREE:-1
- **MotionCategoryType**, **WeaponSilhouetteType**
- **AnimePlayType**, **AnimeType**
- Many local: PreAttackMode, AttackFlowMode/Result, RealExperienceMode, UnitDeathMode, etc.

## Map / Turn / State

- **TurnType**: PLAYER:0, ENEMY:1, ALLY:2
- **StartEndType**, **MapStateType**
- **BadStateFlag** (PHYSICS/MAGIC/ITEM/WAND bits), **BadStateOption**
- **StateAutoRemovalType**: NONE:0, BATTLEEND:1, ACTIVEDAMAGE:2, PASSIVEDAMAGE:3
- **MapType** (NORMAL/EXTRA/QUEST)

## Result / Flow / Enter

- **EnterResult**: OK, NOTENTER
- **MoveResult**: CONTINUE, END, SELECT, CANCEL
- **CycleResult**, **EventResult**
- **ScrollbarInput** etc.

## Graphics / Media / UI

- **GraphicsType**, **MediaType**, **UIType**
- **ResourceHandleType**: ORIGINAL:?, RUNTIME:?
- **BlendMode**, **CompositeMode**, **GradientType**, **InterpolationMode**
- **MusicPlayType**, **MusicStopType**, **SpeedType**
- **MessageLayout**, **FaceVisualType**, **CommandLayoutType**, **CommandActionType**
- **AppScreenMode**, **LanguageCode**

## Flags (bitwise common)

- **UnitFilterFlag**, **PlaceEventFilterFlag**, **TrophyFlag**, **ClassOptionFlag**, **ClassMotionFlag**, **DifficultyFlag**, **KeyFlag**, **UnitStateChangeFlag**, **PassableDirectionFlag**, etc.

## Other Important

- **MatchType**: MATCH:0, MISMATCH:1, MATCHALL:2, MISMATCHALL:3 (used with aggregations)
- **NewSkillType**: NEW:0, POWERUP:1
- **FusionType**, **FusionActionType** (CATCH/RELEASE/TRADE), **FusionReleaseType**, **MetamorphozeActionType**, **MetamorphozeCancelFlag**
- **SaveCallType**, **RestSaveType**, **RestAutoType**, **ClearPointType**
- **SystemSettingsType**, **InfoWindowType**, **SpeakerType**, **TrophyTargetType**
- Value sentinels: `AIValue.MAX_MOVE`, `WeaponLimitValue`, `ChronicInjuryHp`, `MapIdValue`, `ItemIdValue`, `MotionIdValue`, `XPoint`/`YPoint` arrays.

## CUSTOM Ranges (for Plugin Authors)

- Scenes: 20–49999
- ItemType/SkillType/PlaceEventType/PatternType: 100+
- Command layouts, repeat move types, etc. have CUSTOM slots.

Check the source `constants-enumeratedtype.js` comments for exact safe ranges and "使用してよい" notes.

## Usage Tips

- Pass directly to engine methods (e.g. `generator.unitStateAddition(unit, state, IncreaseType.INCREASE, false)`)
- Bit flags: `flag & UnitFilterFlag.PLAYER` or `|=` to combine.
- In comparisons: `if (itemType === ItemType.CUSTOM) ...` or `if (option === WeaponOption.NOGUARD)`
- For custom types, define your own constants object in your plugin IIFE and use consistently.
- Many "値" in api docs correspond 1:1 to these (e.g. DirectionType, TurnType).

## See Also

- The base constants-enumeratedtype.js (read the whole file once; it is the single source of truth with comments explaining intent)
- Method signatures in the API reference documentation (they refer to values such as "DirectionType値")
- Individual pages (calculators use TrueHitValue / WeaponOption / InvalidFlag heavily; items use ItemType / RecoveryType; AI uses PatternType / Move*Type / AIDisableFlag; events use all the Event* types).
- Base files such as singleton-system.js and files under attack/ for local enums that extend these.

When adding new custom behavior, pick a high number in the CUSTOM range and document the constant in your plugin header. Re-check this file (and the constants source) after engine updates.