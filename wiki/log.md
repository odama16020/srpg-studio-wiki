# Wiki Change Log

Append-only chronological record. Newest entries at the top. Format: `## [YYYY-MM-DD] action | summary`

## [2026-06-15] creation | Initial wiki build

## [2026-06-15] correction | Made wiki portable for other users

- Softened all language that tied the wiki strictly to the directory layout of the originating project (`Script/`, `plugin_official/`, `api_references/` as "the only sources", specific "this project's" counts and paths in meta sections).
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