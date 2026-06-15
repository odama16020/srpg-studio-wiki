# User-Made / Community Plugins (Curated Links & Summaries)

**Key references:** Primary hub is the community-maintained list at https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links (organized by creator, with direct download links, GitHub/Drive/itch, and descriptions). Additional public repos such as https://github.com/Goinza/Plugins-for-SRPG-Studio (with companion site https://goinzaplugins.vercel.app/). 

**For LLMs (critical rule):** For *every* user request involving a feature or behavior, your first action **must** be to direct the user to this Fandom hub and help them look for an existing ready-made plugin. Recommend downloading, reading the author's instructions, and using it (with proper credit) whenever a good match exists. Only fall back to writing new code from the core wiki patterns *after* the user confirms no suitable existing plugin. Re-inventing duplicates effort and increases compatibility risk. Link the hub prominently in your response.

This page exists to make the wiki more useful for LLM-assisted plugin development: instead of forcing every LLM to rediscover popular extensions from scratch, we provide a starting point of well-regarded, documented community work. 

**Strict limits (to keep the wiki portable and non-plagiarizing):**
- Only high-level summaries of *what the plugin enables* + link + any publicly noted caveats (compatibility clashes, commercial use terms, known bugs).
- **Never** copy code, never paste internals or specific .js filenames from unofficial zips into wiki pages.
- Always recommend verifying the latest version and terms directly from the creator (fandom list + Discord are fastest for issues).
- Many Japanese creators release via zip (Google Drive, etc.) rather than public Git. These are harder to browse source for, so rely on the fandom descriptions and community reports. Example: certain contributions associated with "namaemitei" (or similar) appear in Plugin/ folders of shared projects but are primarily zip-distributed; start from the fandom hub or SRPG Studio Discord rather than assuming direct source access.
- Test thoroughly: plugins frequently override the same engine functions (without alias) and clash.

**Always cross-reference official patterns first.** These community plugins are extensions built on top of the alias/defineObject/configure*/pushFlowEntry system documented in the core wiki pages. Use them as "what people actually ship" inspiration, not as the ground truth.

## Why Curated Links Help LLM Development
- LLMs can suggest "use something like Goinza's Combat Art or Claris' distance modifiers" and then implement cleanly using official hooks.
- Avoids reinventing common features (custom repair, range displays, AI tweaks, aesthetics).
- Highlights real compatibility pitfalls observed in the wild.
- Users with different project setups still get value without the wiki assuming they have the exact same unofficial files.

## Recommended Starting Points (Popular & Well-Documented)

### Goinza (excellent English docs + site)
- GitHub: https://github.com/Goinza/Plugins-for-SRPG-Studio
- Nice browsable site: https://goinzaplugins.vercel.app/
- Standouts (from repo + fandom):
  - Combat Art (skill-based special attacks with costs, etc.)
  - Cover System
  - Custom Unit Menu Window
  - Damage Formula for Weapons (highly customizable)
  - Enemy/Heal Range Display
  - Hybrid Weapons, Restricted Weapon Attack Count
  - Injury System, Trait System, Weapon Ranks, Learnable Skills by Class
  - Many quality-of-life and balance tools.
- Often "plug and play" with clear readme. Good model for structure.

### Claris (RogueClaris)
- GitHub: https://github.com/RogueClaris/SRPGStudioScripts/
- Heavy on aesthetics, UI, text, audio, graphics mods (Charchip Mod, Custom Animation, Face Front, Idle Animation, Colored Item Names, Durability Gauge, etc.).
- Mechanics: Distance Accuracy (inspired many others), Splash Damage (reverse-engineered from o-to), Weapons & Items as Original Data (fork of Goinza).
- Many are "plug and play" or require only custom params. Great for visual/feel improvements.

### CeruleanAcorn
- GitHub: https://github.com/CeruleanAcorn/SRPG-Studio-Plugins
- Focus on mechanics tweaks: Only Retaliate (custom param or skill), Army Switch Strike, Dim Face after Unit Move, Disappear on Hit, Equip Discards Unequipped Weapons, Just Enough Healing, Limit EXP by Level, Shatter Strike, Spread Penalty, Superiority Complex (weapon type / stat target matching), Weakest Link.
- Detailed notes on required supporting plugins and alias issues.

### Blindcoco
- GitHub: https://github.com/blindcoco/srpgstudio
- Miscellaneous fixes & utilities: Combat Art Session Var Reset, Get Unit ID by Custom Skill Label, Lower State Turn Count on Unit Turn, Targeted Repair Item, Unit Command Placement.
- Skills: Distance Damage Modifiers, Granular Coefficient, Ploy Skills (FEH-style).

### 2chang
- GitHub: https://github.com/2changi/SRPG-Studio-Plugins
- Attack Target Conditions, Random Unit Call.

### Others frequently mentioned / useful
- CrazyLi825: Custom Defense, etc.
- Ephraim225: Absorb HP Modifier, etc.
- MarkyJoe1990: https://github.com/MarkyJoe1990/SRPG-Studio (controls, assets, plugins)
- Katafract: itch.io hub
- Kirby5790 (Sir Dude), etc.

See the full Fandom page for the current exhaustive list by creator (2chang, Blindcoco, CeruleanAcorn, Claris, CrazyLi825, Goinza, ... and many more). The page is actively maintained by the community.

## Additional Japanese Community Repositories (public GitHubs)

These are primarily Japanese-language public repos providing many practical extensions. Browse their READMEs, folder names (often descriptive of the feature), and source headers for usage (always respect the individual license/terms per repo). High-level capabilities noted for LLM inspiration; always reimplement using official alias/defineObject/custom patterns.

- **QBE256/ExtraPlugin** — https://github.com/QBE256/ExtraPlugin
  - 300+ commits, many small/niche single-feature scripts (often with Japanese filenames describing effect).
  - Examples: CSV読み込み (CSV load for data), モーションボイス (motion voice config/sample), 待機でターン消費ステート (wait consumes state turns), 成長パターン指定, 杖魔法リアル戦闘化, 漢字カナ変換, 自動中断セーブ, クラスチェンジモーション変更, グローバルスイッチ/変数でスキル・アイテム発動条件追加, クリティカルが出ない武器, HP1最小ダメージでとどめ, 遠見 (distant view), 復活スキル, ストックコマンド, テレポート制限, 難易度別ユニット設定, ユニット最大Lv, 武器種表示, スロリアプローチAI, etc.
  - Reusable dev note (from its README): Custom parameters for multiple plugins on the *same* editor object (weapon/unit/etc.) **must be written as keys inside one single outer `{}` block** (merge them). Separate objects will not combine. See https://srpgstudio.com/developer/custom.html . Reference the repo's custom param example when documenting your own plugins' data requirements.

- **RantaroGames/SRPG_Studio** — https://github.com/RantaroGames/SRPG_Studio
  - Very large collection (hundreds of commits); permissive terms (mod/re-dist ok, no paid distribution, credit not required).
  - Broad coverage: AI索敵範囲拡大, minimap, skill info effect display, custom weapon items, doping item LV limit, weapon crit/attribute change, event save complete judge, batch item inc/dec commands, many info windows (acquirer name, terrain info, state grant, break msg, item count change), proxy combat skill, distance-based dmg reduction, custom state, state that seals specific item types only, HP0 surround dmg skill, 実績リスト screen, config enemy turn skip limit, message layout per speaker, map display tweaks (AI stolen item name, key help, class type icon, terrain coord, location event icon, enemy action order), フュージョンリリース command, etc.
  - Reusable dev note: Dedicated `Tips/` folder with shared utilities. `originalMethod.js` is a collection of helper functions designed for "イベントコマンドの実行条件 > スクリプトを条件にする" (or Script Execute code-execute); also provides control objects (e.g. UnitCustomParameterContorol) for getting/setting custom params and OriginalData values from events without hardcoding. Multiple `*_mod.js` files demonstrate patching `drawScreen*` (e.g. ExperienceDistributionScreen.drawScreenBottomText, ShopLayoutScreen_drawScreenTopText) and similar for help text / layout adjustments on specific screens. See Tips/Readme.md in the repo.

- **iroha-code/SRPGStudio_Plugin** — https://github.com/iroha-code/SRPGStudio_Plugin
  - Quality-of-life and system plugins. Install via copy-paste into Plugin/ (per its README).
  - Examples: 視界システム (vision system), 天候プラグイン (weather), 武器・杖選択画面で範囲表示, 戦闘予測で武器変更, 付与ステート詳細表示, レベルアップ時に装備可能熟練度到達武器表示, マップチップ発光, 5段階メッセージスピード, 任意アニメ呼び出し, 慣性カーソル, 戦闘準備BGM続行, 目標確認で変数利用, 難易度別敵ステータス割合, スペースキーターン終了, アイテム利用AI移動制御, ゴールドカウントアップ速度調整, スキル/アイテム増減時説明表示, タイトルアップデートチェック, マップコマンド目標確認同時表示, マップユニットHP(数値+ゲージ), マップ移動時ステート継承, キャラクリエート, ひとことコメント, developer help function (【開発者向け】ヘルプ機能.js).
  - Reusable: Vision and weather as self-contained systems; range/weapon select UI enhancements; developer tooling.

- **RYBA549/RYBA_SrpgStudio** — https://github.com/RYBA549/RYBA_SrpgStudio
  - MIT license. Focused on advanced mechanics and controls.
  - Examples: Duplication (BookmarkUnit, Enemy, PhantomUnit), EventjumpNeo, ForceAbsorption, HpRecoveryMovedCommand, ItemSentenceTargetAggregation, RecoveryItemAndBadState, SkillHpCheker, StateQuickDecreaseTurn, WakeInjured, WandUseMoved, AlignmentControl / AlignmentControl2 / AlignmentControlEx, CatchUnitAssign, CounterStopState, DurabilityControl, EffectiveEx, FusionParamBonusSkill, Necromance, ScrollCursorSpeed, SkillCondCompatible, SkillGetItemAgainBlock, UndoLoadControl, CallingExperienceDistributionScreen, CommunicationSecond, Plugin_AnalyzeTextNPC.
  - Reusable for unit duplication mechanics, alignment/fusion/necromance variants, durability/state turn control, effective extensions, undo/load guards.

- **CordialBun/srpg-studio-plugin** — https://github.com/CordialBun/srpg-studio-plugin
  - Battle mechanics "System" plugins with detailed Japanese headers in source (setup via state ID, custom skills/params on weapons, coexistence flags).
  - Standouts:
    - BreakSystem: Advantage (相性有利) hit applies configurable "Break" state (resist via custom skill keyword "resistBreak"; optional skill "break" or weapon custom `isBreakWeapon`; AI can prioritize breakables via score multiplier). Broken units cannot counter (affects VirtualAttackControl, AttackEvaluator, AttackChecker, StateTurnFlowEntry, AIScorer.Weapon). Recovers on owner phase. Coexists with WaitTurnSystem option.
    - ChargeWeapon, DelayAttack, GrazeHitSystem (grazing hits), Obstruct, RemoveEquipment, RewindTimeSystem, SpeedTaker, VaryEffectiveFactorSkill, WaitTurnSystem.
  - Reusable dev model: Heavy alias use on core battle objects (AttackEvaluator.*, VirtualAttackControl, AIScorer, State*FlowEntry, AttackChecker), custom-param/skill-keyword driven activation, state ID config, AI scorer tweaks for new priorities, explicit inter-plugin coexistence handling. Excellent template for "add new combat rule" style plugins. Commercial/non-commercial use allowed with credit (keep author name in comments).

When studying these (or any user-made), always:
- Alias the original when overriding (even if the example appears to replace outright).
- Namespace custom keys/keywords.
- Test combinations (many override Calculator/AI/Misc or screens without alias and clash).
- Verify latest license/terms directly (commercial use is common but not universal).
- Re-implement the *idea* using the official patterns in this wiki rather than copying.

These repos fill many practical gaps (UI tweaks, niche data systems, new battle states/mechanics, event helpers) that complement the official srpgs_official_plugin examples. Use them to discover what designers actually want, then ground the implementation in the core alias + custom + flow patterns documented elsewhere in the wiki.

## Handling Hard-to-Check / Zip-Only Releases
Some popular plugins (including certain namaemitei / "名前未定" contributions and Japanese community works) are distributed primarily as .zip files placed into a project's Plugin/ folder rather than public Git repos with browsable source.

- **Best discovery path**: Start at the Fandom User-Made Plugin Links page (it aggregates links even for zip/Drive releases). SRPG Studio Discord for questions/compat.
- When reverse-engineering or porting ideas from such zips for your own work, treat them as inspiration only. Re-implement using the official alias + configure* + defineObject patterns documented here.
- Never assume a specific zip filename or author credit style will be stable; document your own work with clear sources in the official patterns.

### Notable Examples: o-to and 名前未定（仮）
These two are frequently referenced in Japanese community and Fandom as foundational for many features (splash/distance damage inspirations, skill extensions, gauges, UI, battle behaviors, etc.). Their materials are typically Google Drive zips linked from blogs or provided via Ci-en/Fandom, making direct source browsing require download (use public descriptions + Fandom summaries).

- **o-to (https://o-to-no-hokanko.blogspot.com/2015/10/srpg-studio.html)**: Long-running "保管庫" (archive) with detailed public blog post describing the entire collection + Google Drive zip link (updated periodically; terms updated ~2025/09 to require credit + usage report via email, no reply expected). Explicit compat notes with other creators (e.g., 名前未定（仮） integrated cal, さんごぱん WaitTurnSystem).
  - Major public capabilities (from blog descriptions): 
    - スキル発動条件追加 (rich skill activation extension): dozens of custom param options for rates/conditions (HP%, turns/multiple, distance, surround allies/enemies, fusion state, equipped/ target weapon types, states, EP/FP consumption, command skill usage + cooldowns, priority for duplicate same-type skills like different multi-attacks, forced skills per class/race, pre-checks, etc.). Also item/weapon param bonus reflection toggle (perf note).
    - スキル追加: FE-like (破天/status-to-damage, 復讐/revenge low-HP powerup), absorb HP on hit, guaranteed crit + multiplier change, ignore DEF, random 2-5 attacks, continue battle after kill, reduce enemy attack rounds on hit, damage guard (AoE version), preemptive swap, min/max hit rate caps, etc.
    - 範囲攻撃アイテム (AoE attack items): full package with power scaling (unit ability + weapon), hit/miss/ use effects (anime, states multi with prob, terrain chip change, % damage-to-HP recovery), range shapes (cross, breath), filters (editor or custom), range display toggle (shift key), recoil damage. Notes that "pseudo item skill" behaviors (skill-used-as-item without owning item) require other plugins (e.g., 名前未定) and this one alone doesn't provide that.
    - MP(EP) & 必殺ゲージ (EP/FP resource system): add EP (MP-like) + FP (crit-like, per-turn charge) to units; consumption on move/ weapon/item use (configurable per command); recovery on hit/miss/take damage/avoid; turn-start state grant at thresholds; base data or script execute for max/current/recovery rate control; unit event commands for consumption/condition.
    - バトルトーク: battle quotes (wait/victory/damage/avoid/death) with expression change; per-weapon attack lines, pursuit lines; "script execute" integration for Custom.ini lines.
    - 敵行動スコア上位ランダムピック: instead of always picking the single highest AI score action, pick randomly from top scorers (for variety with multi-target or multi-weapon enemies, e.g., high-dmg vs status weapon).
    - ダメージ補正・誤差: level-based correction and variance (for campaigns with very high HP pools).
    - 出撃禁止設定: via map custom param or script execute per-unit.
    - インターフェイスをウィンドウサイズに合わせる: auto layout adjustment for small maps vs window.
  - Fandom notes: o-to credited as original for splash damage concepts (Claris has English-friendly re-implementation page); MP/SP gauge script has community English replacement ("Mana System").
  - Reusable patterns visible in public docs: extremely data-driven skill conditions via custom (complements official custom-skill + keyword dispatch); resource gauges as unit custom + calculator/flow hooks; AoE item as custom item + effect range + dynamic generator; AI score post-processing for non-deterministic choice.

- **名前未定（仮） (https://ci-en.net/creator/4919/article)**: Highly prolific creator on Ci-en (hundreds of plugin articles since ~2020, monthly "プラグインリスト" summary posts listing updates + individual article pages with details). Materials often zips linked in articles (free plan or support access); commonly bundled into shared project Plugin/ folders or Fandom-listed. "00_" prefixed files often serve as compat layers or core definitions.
  - Scope from public lists/titles (very active monthly releases): 
    - 統合Cal (integrated calculator, major; o-to explicitly notes compat loading order and conflict mitigations in its archive).
    - 武技っぽいの (martial-arts-like skill system; integrates with o-to skill conditions extension).
    - 疑似アイテムスキル (pseudo item skills: key for "use skill as if item" without actual item ownership, enabling many custom command/skill behaviors seen in Japanese games).
    - Extensive UI/display: backlog (name display, page-by-page, large face, width/scroll), status screen customs (HP bars/gauge positions, item columns shrink, icons, skill help toggle, class desc, support values, state display, weapon icons), map unit window (HP custom, state custom, fusion info, item display), terrain window (HP recovery display, bonus/support skill, coord), goal/objective confirm tweaks (non-hidden enemy count, etc.).
    - Battle/real: attack window plus, combat result prediction mods (motion image, custom), real battle position/motion/alpha/ effect continue/pinch UI/background anime, dual wield (二刀流), super bow, etc.
    - Mechanics: 範囲を持つステート (AoE states), re-move custom, steal weight by build (体格), stop motion command, race-specific state effect mods, min 1 damage weapons, various event commands (line message, etc.), growth/exp tweaks, hard boost, fusion related, shop/layout, message title/control, stock custom, etc.
    - AI: enemy move-range target random selection.
    - Many patches: TextRenderer error, item exchange, system-interruption, guest item restrictions, etc.
  - Fandom: Aggregates many under Japanese creators; o-to and others reference namaemitei works for compat.
  - Reusable: "pseudo" patterns for extending item/skill/command overlap; heavy focus on display polish (status/map/terrain/backlog windows via renderer or draw hooks); monthly changelog style for tracking; lots of small robust fixes for edge cases (save, load, skip, multi-plugin).

For both: public blog/Ci-en article titles + Fandom provide excellent high-level overviews and custom param examples even without unzipping. Download zips for full headers/READMEs if needed for a project. Check Fandom for per-plugin English notes or ports where available. Always test coexistence (especially calculators, AI, status screens, real battle). These (plus GitHub Japanese repos) represent the bulk of "real" Japanese dev patterns used in actual games.

## Compatibility & Best Practices (Applies to All Community Plugins)

## Compatibility & Best Practices (Applies to All Community Plugins)
- Plugins often override the same engine functions (Calculator.*, Miscellaneous.*, etc.) without using alias. Alphabetical load order or explicit alias discipline matters.
- Test in isolation first, then in combination.
- Check the plugin's readme for "Functions overridden without an alias" lists.
- Many creators explicitly allow commercial use with credit; always verify per-plugin.
- Global Parameters vs per-object custom parameters: know the difference.
- For range/distance/accuracy mods: many variants exist (Claris, Blindcoco, others); pick one and be consistent.

## See Also
- [LLM-INSTRUCTIONS.md](../LLM-INSTRUCTIONS.md) — updated sourcing rules
- [getting-started.md](../getting-started.md)
- [overriding-patterns.md](../overriding-patterns.md) — the foundation everything builds on
- [references/official-plugin-patterns.md](official-plugin-patterns.md) — official examples (the "blessed" reference)
- Fandom hub (primary community list): https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links
- SRPG Studio University / Discord for support

When adding new user-made work to this page, prefer ones with English docs, active maintenance, or that fill clear gaps (e.g. new command types, advanced AI, balance tools). Link the fandom entry + GitHub when available. Keep summaries short and capability-focused.