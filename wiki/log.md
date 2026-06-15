# Wiki Change Log

Append-only chronological record. Newest entries at the top. Format: `## [YYYY-MM-DD] action | summary`

## [2026-06-15] creation | Initial wiki build

- Created full initial wiki under `srpg-studio-wiki/wiki/` (18+ .md files) for LLM-assisted SRPG Studio plugin development.
- Structure: mostly flat + `references/` and `advanced/` subdirs (hybrid per planning input).
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
- All content strictly sourced from `Script/` (v1315 info.ini + 156 .js), `plugin_official/` (~200 .js), `api_references/` (14 .html). No specific unofficial plugins named.
- Heavy emphasis on ES5 rules, alias discipline, createObject factories, .custom guards, configure*/pushFlowEntry hooks, DynamicEvent, the three AI objects, 6 custom item bases, StraightFlow, StructureBuilder, etc.
- Detailed excerpts, code skeletons (always IIFE + ES5), tables for enums/results, "Sources:" headers, cross-links, "See Also".
- Follows llm-wiki.md pattern (persistent indexed artifact, index.md as content catalog, log.md, LLM-INSTRUCTIONS as schema).
- Exploration performed via list_dir, grep, read_file (with limits), multiple explore subagents for parallel coverage of Script structure, calculators/AI/items/commands, api HTMLs, base patterns, official categories, enums, flows, etc.
- Plan written and approved before implementation.
- Git commit + push to odama16020/srpg-studio-wiki (using gh auth status + git push per preference).

## Future entries go above this line (newest first)