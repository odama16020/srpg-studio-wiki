# Wiki Change Log

Append-only chronological record. Newest entries at the top. Format: `## [YYYY-MM-DD] action | summary`

## [2026-06-15] expansion | Added 5 Japanese public plugin repos + extracted general dev procedures

- Added dedicated "Additional Japanese Community Repositories (public GitHubs)" section to references/user-made-plugins.md covering https://github.com/QBE256/ExtraPlugin , https://github.com/RantaroGames/SRPG_Studio , https://github.com/iroha-code/SRPGStudio_Plugin , https://github.com/RYBA549/RYBA_SrpgStudio , https://github.com/CordialBun/srpg-studio-plugin .
- High-level summaries of capabilities (niche utilities, UI windows, systems like vision/weather/alignment/necromance/duplication, battle extensions such as Break/Graze/Charge/Delay/Obstruct/Rewind/WaitTurn, AI tweaks, etc.) + links + license notes + commercial use notes.
- Extracted reusable general development procedures observed across their sources (without copying code or internals) and cross-referenced from core pages:
  - Custom param combining into single top-level {} (QBE256 README; added to custom-parameters-keywords-originaldata.md).
  - originalMethod.js style helpers for "スクリプトを条件にする" / event code-execute + drawScreen* mod examples for help/layout on specific screens (Rantaro Tips/; added notes + links to events-dynamic-script.md and ui-rendering-windows.md).
  - Full battle mechanic systems via deep alias on AttackEvaluator / VirtualAttackControl / AIScorer / StateFlow (CordialBun); AI score multipliers and priority for custom states (added to battle-flows-and-attacks.md and ai-enemy-behavior.md).
  - Vision/weather as systems, developer help tools, range UI, class motion, duplication mechanics (iroha, RYBA; referenced via user-made page).
- Updated index.md (last major update), LLM-INSTRUCTIONS.md / getting-started.md / official-plugin-patterns.md (fixed stale community-plugins.md refs to references/user-made-plugins.md), added cross links in See Also sections.
- All additions keep strict policy: links + high-level "enables X" only; no code, no unofficial filenames in core pattern docs; always ground new work in official + alias discipline.
- Appended this log entry. Fandom hub remains the primary exhaustive list.

## [2026-06-15] expansion | Policy update to include curated User-Made Plugins links

- Changed long-standing strict "no mention of any unofficial plugins" policy (in LLM-INSTRUCTIONS.md, index.md, getting-started.md, etc.).
- Rationale: providing high-level links and summaries of popular, well-maintained community plugins (from Fandom User-Made Plugin Links hub + notable public GitHub repos) makes the wiki far more useful as a practical development resource for LLMs and humans. LLMs can now recommend battle-tested extensions, avoid reinventing wheels, and build upon patterns from real projects.
- New rule: Curated links + capability summaries + caveats (compatibility, commercial use, known clashes) are allowed and encouraged in a dedicated page. Never copy code, never deep-dive internals of unofficial zips, never hardcode specific user filenames in core pattern pages. Always ground new work in official Script + srpgs_official_plugin + API.
- Added/updated wiki/community-plugins.md (or equivalent) with:
  - Link to primary hub: https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links (organized by creator, with download links and descriptions).
  - Highlighted public GitHubs (e.g. Goinza/Plugins-for-SRPG-Studio with its nice vercel site https://goinzaplugins.vercel.app/ covering Combat Art, Cover System, Custom menus, Damage formulas, etc.).
  - Other notables from the list: 2chang, Blindcoco, CeruleanAcorn, Claris (many aesthetics/UI/mechanics), CrazyLi825, Ephraim225, MarkyJoe1990, etc.
  - Notes on Japanese community zips (e.g. certain namaemitei contributions or o-to reverse-engineered plugins) — often only available as .zip in Plugin/ or Drive, hard to browse source directly. Recommend starting from Fandom list + SRPG Studio Discord for latest. Some have been re-implemented or documented via English ports on GitHub.
  - Advice: always test combinations (plugins frequently clash on shared functions like calculate* or without alias overrides), check creator terms for commercial use, prefer "plug and play" or well-documented ones first.
- Updated all policy statements and references.
- This makes the wiki a better "one-stop" for LLM plugin dev: official patterns + real-world community extensions via links.

## [2026-06-15] creation | Initial wiki build

## [2026-06-15] correction | Made wiki portable for other users

- Softened all language that tied the wiki strictly to the directory layout of the originating project (`Script/`, `srpgs_official_plugin/`, `api_references/` as "the only sources", specific "this project's" counts and paths in meta sections).
- Reframed sourcing notes, "Sources:" headers, LLM-INSTRUCTIONS.md, index.md, log.md, getting-started.md, and README to describe the content as informed by "the base overridable scripts", "official example plugins", and "the engine's API reference documentation".
- Specific file names (e.g. calc-goodweapon.js, singleton-calculator.js) are retained where they provide practical guidance, as these are the actual names users will encounter.
- Goal: other users can feed the wiki/ to their LLM even if their project folder structure or selection of official plugins differs.
- No change to the actual technical content or examples.

- Created full initial wiki under `srpg-studio-wiki/wiki/` (18+ .md files) for LLM-assisted SRPG Studio plugin development.
- The wiki is intended to be portable for other users who may have different project folder layouts or not all official plugins installed.
- Structure: mostly flat files + `references/` and `advanced/` subdirs.
- Pages created (in logical order):
  - es5-compatibility.md (critical; with evidence from searches)
  - overriding-patterns.md
  - defineobject-flows-and-helpers.md
  - custom-parameters-keywords-originaldata.md
  - calculators-damage-hit-critical.md
  - item-system-custom-items.md
  - ai-enemy-behavior.md
  - commands-unit-map.md
  - battle-flows-and-attacks.md
  - events-dynamic-script.md
  - root-sessions-api.md
  - assets-and-datalists.md
  - enumerated-types.md
  - ui-rendering-windows.md
  - utilities-and-structs.md
  - references/api-surface-glossary.md
  - references/official-plugin-patterns.md
  - advanced/highlevel-and-composition.md
  - getting-started.md
  - LLM-INSTRUCTIONS.md (maintenance schema + workflow)
  - index.md (catalog + navigation)
  - log.md (this file)
- Content informed by the base overridable SRPG Studio scripts (Script folder), official example plugins, and API reference documentation. Specific unofficial/community plugins are not named or directly referenced.
- Heavy emphasis on ES5 rules, alias discipline, createObject factories, .custom guards, configure*/pushFlowEntry hooks, DynamicEvent, the three AI objects, 6 custom item bases, StraightFlow, StructureBuilder, etc.
- Detailed excerpts, code skeletons (always IIFE + ES5), tables for enums/results, "Sources:" headers, cross-links, "See Also".
- Follows llm-wiki.md pattern (persistent indexed artifact, index.md as content catalog, log.md, LLM-INSTRUCTIONS as schema).
- Exploration performed via list_dir, grep, read_file (with limits), multiple explore subagents for parallel coverage of Script structure, calculators/AI/items/commands, api HTMLs, base patterns, official categories, enums, flows, etc.
- Plan written and approved before implementation.
- Git commit + push to odama16020/srpg-studio-wiki (using gh auth status + git push per preference).

## Future entries go above this line (newest first)