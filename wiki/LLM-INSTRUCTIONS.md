# LLM Instructions for Maintaining This Wiki

**This file exists so future LLM sessions (and the human curator) know exactly how to evolve the wiki when new source material arrives.**

## Core Rules (Never Violate)

1. **Sourcing focus**:
   - Prioritize the base overridable script files (the Script folder and its subfolders in a typical SRPG Studio project), the official example plugins, and the engine's API reference documentation (the HTML files).
   - You may also consult the general llm-wiki.md pattern file (at the project root) and this wiki itself for tone, structure, and maintenance conventions.
   - Unofficial/community plugins (User Made Plugins) can be read privately for implementation ideas and to discover additional patterns. A curated, high-level list of popular/recommended ones (with links to download pages, GitHub, or the Fandom User-Made Plugin Links hub, plus short capability summaries and compatibility notes) is now permitted in the wiki (see references/user-made-plugins.md). This makes the wiki more practical for LLM-assisted development of new plugins, without violating portability. Never copy-paste code or low-level internals from unofficial plugins into wiki pages; only high-level "this enables X, see link". Always prefer grounding in official Script + srpgs_official_plugin + API.
   - Every factual claim, method name, code example, or pattern should be grounded in the official base scripts, official plugins, or API docs. Use "Key references:" or "Sources:" style notes on pages.

2. **ES5 compatibility is sacred**:
   - SRPG Studio plugins run in a very old JS environment.
   - All code examples in the wiki **must** be valid ES5 (var only, classic for loops, function, indexOf instead of includes, no arrows/templates/classes/for-of/spread/defaults, etc.).
   - Every page that shows code must link to or reference es5-compatibility.md.
   - When a modern construct would be clearer, immediately show the compatible equivalent.

3. **Wiki is the single source of truth for future LLM plugin generation**:
   - Future agents should be able to generate correct plugins after reading the wiki plus the base Script files, official plugins, and API reference docs, without having to rediscover patterns from scratch on every session.
   - Keep cross-links dense. Update the index on every change.
   - The wiki is designed to be portable: users may have different folder layouts or may not have all official plugins installed in their projects. Avoid hard references to any one specific project's directory layout.

## Directory & File Conventions

- Mostly flat under `wiki/` for simple LLM navigation (`index.md` first, then descriptive names).
- Only two subdirs: `references/` (glossary + patterns) and `advanced/` (high-level graphics/scenes).
- New pages should have clear, hyphenated names.
- Start every substantial page with a **Key references:** or **Sources:** note that points to the relevant base script files (e.g. singleton-calculator.js), official plugin examples (e.g. calc-goodweapon.js), and/or sections of the API reference documentation. Be specific enough to be useful, but do not assume the reader has the exact same folder layout as the one used to create the wiki.
- Use relative links: `[ES5 rules](es5-compatibility.md)`, `[DamageCalculator](calculators-damage-hit-critical.md#damagecalculator)`.
- Code blocks are always ```js with ES5 code wrapped in IIFE where appropriate.
- Use tables for enums, result values, and method lists where helpful.
- Keep a "See Also" section at the bottom of every page.

## Maintenance Workflow (Ingest New Material)

When updates to the base scripts or new official plugins become available (new engine version, additional official plugins released, etc.):

1. **Read the index** first (`wiki/index.md`) to understand current coverage.
2. **Identify impacted areas** by grepping the new sources for new objects, _configure*, configureCommands, pushFlowEntry sites, new calculators, new Base* implementations, new root/session methods, new enums, etc.
3. **Update or create pages**:
   - Core patterns (alias, defineObject, flows, custom params) if the mechanism changed.
   - Domain pages (calculators, items, AI, commands, battle, events, UI, etc.).
   - references/official-plugin-patterns.md — add new categories or note new objects they touch.
   - references/user-made-plugins.md — when major new community plugins or repos become prominent (update links, summaries, caveats from Fandom hub + GitHubs). Do not add code or deep details.
   - enumerated-types.md if new constants appear.
   - api-surface-glossary.md for new high-usage surface.
4. **Update cross-links** and "last updated" style notes in index.md.
5. **Append to log.md** with a dated entry (use the format `## [2026-06-15] ingest | Short description of change` at the top of the file). Include which source files triggered the update.
6. **Health check** (lint-like):
   - Search for contradictions (same object described differently on two pages).
   - Look for orphan pages (no inbound links from index or other pages).
   - Ensure every code example still follows current ES5 rules (re-verify with grep on sources that 0 modern syntax is used in core + official).
   - Confirm all "Sources:" headers are still accurate.
   - Flag any area where Script now has significantly more implementation detail that should be surfaced.

## Schema for New Pages

Minimal structure:

```markdown
# Page Title

**Sources:** Script/xxx.js, Script/yyy.js, srpgs_official_plugin/foo.js, api_references/bar.html, ...

Intro paragraph.

## Section

Details + code (ES5) + warnings.

## See Also

- [other page](...)
```

For reference/glossary pages, be explicit that the HTML + Script sources are the ground truth.

## Query / Answer Workflow (When User Asks a Plugin Question)

1. Read `wiki/index.md` to find relevant pages.
2. Read the 1-3 most relevant pages fully.
3. If needed, read the cited source files in Script/srpgs_official_plugin/api_references for precise signatures (use read_file with limits or grep).
4. Check references/user-made-plugins.md (or the Fandom hub it points to) for whether a popular community solution already exists for the feature (link it in the answer for the user).
5. Synthesize an answer that cites the wiki pages + specific source files/lines where possible.
5. If the answer requires a new code pattern not yet in the wiki, also propose (and later perform) the wiki update as part of the same task.
6. Always remind about ES5 rules and the alias discipline.

Good answers compound the wiki: after helping the user, file the synthesized knowledge back as a small update + log entry if it was missing or unclear.

## Log.md Discipline

- Append-only, newest at top.
- Consistent prefix: `## [YYYY-MM-DD] action | summary`
- Actions: creation, ingest, update, health-check, expansion, correction.
- Keep entries short but useful for an LLM to understand recent history without reading everything.

## Health / Lint Commands (Run These Periodically)

Use terminal / grep inside the wiki dir + source dirs:

- Verify no modern JS syntax leaked into wiki code blocks (search for let/const/=>/` in the .md files' code sections).
- Count inbound links from index.md vs actual .md files.
- Grep sources for new "_configure" or "configureCommands" or "pushFlowEntry" sites not yet documented.
- Check that every topical page still has a "Sources:" header.

## Tone & Language

- English (as decided for the wiki).
- Precise, example-heavy, LLM-friendly (short paragraphs, bullets, tables, explicit "do this / never do that").
- Repeat critical rules (ES5, alias call, typeof guards, createObject) rather than assuming the reader remembers.
- Be honest about areas that are "see the source + similar patterns" rather than claiming full coverage.

## When in Doubt

- Prioritize accuracy over completeness.
- Add a "TODO / gaps" note at the bottom of a page instead of guessing.
- Re-read llm-wiki.md (project root) for the overall philosophy: the wiki is a persistent, compounding artifact maintained by the LLM.
- Ask the human curator narrow clarifying questions before making large structural changes.

## Initial Creation Note (2026-06-15)

This wiki was initially built by studying the base overridable scripts that ship with SRPG Studio (the Script folder and important files inside it), the official example plugins, and the engine's API reference documentation (HTML files). The work focused on extension mechanisms, common patterns, the required ES5 style, and representative official plugin implementations.

A number of pages were produced with detailed explanations and examples. The structure (mostly flat files with a couple of subdirectories for references and advanced topics) was chosen for easy navigation by LLMs and humans.

Future sessions: treat this file as part of the schema. Update it when the maintenance process itself improves. Remember that this wiki is meant to be usable by people whose project directory layout or set of installed official plugins may differ.

The wiki's job is to make generating correct SRPG Studio plugins (in very old JS) as reliable and low-friction as possible for LLMs and humans. Keep it healthy.