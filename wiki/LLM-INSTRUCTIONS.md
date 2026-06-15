# LLM Instructions for This Wiki

**Two main use cases (read the right part for your session):**

- **Most common: Helping a user develop plugins** (everyday SRPG Studio developers using their LLM + this wiki). See the "Usage for Plugin Development" section below first. The focus is low-friction, Fandom priority, official patterns only, and helping users with limited programming/LLM experience.
- **Less common: Maintaining / curating the reference wiki itself** (when new official Script or official plugins (folder name varies by project) material arrives). See the "Maintenance Workflow" section.

This file (plus the root AGENTS.md) is the schema that tells LLMs how to behave with the wiki.

**Primary goal overall:** Make it possible for LLMs to produce correct, compatible SRPG Studio plugins (in very old ES5 JS) reliably, while steering users toward existing community solutions on Fandom whenever possible instead of re-inventing.

## Usage for Plugin Development (Default / Most Sessions — Focus Here for Normal Users)

This is the workflow when a regular SRPG Studio developer is using their LLM (Claude, Grok, etc.) + this wiki to write plugins for their game. Users may have shallow programming or LLM knowledge. Keep instructions simple, patient, and actionable. Provide copy-paste ready things.

### #0 Mandatory Rule — Fandom First (Never Skip)
Before reading wiki pages or writing *any* code for a requested feature:

- Tell the user clearly: "The easiest and best way is usually to use an already existing community plugin instead of writing new code from scratch."
- Direct them to the hub: https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links
- Help search/describe: "Look under [creator] or for keywords like 'range', 'splash', 'gauge', 'custom state', 'AI', 'item'..."
- If a good match: Recommend downloading the plugin, reading its usage (usually in the zip or description), testing it alone first, and crediting the author. This is the recommended path.
- Only after the user says "no suitable one" or "I need something custom / to learn the official way", proceed to use the wiki.

The preference for checking existing community solutions first (via Fandom hub) is noted in AGENTS.md, index.md, getting-started.md, and user-made-plugins.md. Mention the hub when relevant for common features.

### Practical Steps in a Session
1. User makes the wiki files available (folder path, uploads key pages, RAG, or project context) and ideally gives you the root AGENTS.md or the starter prompt from it.
2. Read `wiki/index.md` first to choose the right pages.
3. After Fandom check (above), read 1–3 most relevant wiki pages + any cited official source comments.
4. Synthesize: Explain simply, cite the wiki pages + specific patterns ("see overriding-patterns.md for the alias example").
5. Output complete, ready-to-paste plugin code in the standard official style (header comment with 使用方法, IIFE + alias).
6. Give concrete next steps: "Put the .js in your Plugin folder, set the custom parameters on the unit/weapon as described, test in Test Play with skip modes on/off."
7. Remind core rules every time it matters: ES5, alias the original, typeof guard for .custom, respect skip modes, test combinations.
8. (Optional for sustained use) If the user wants personal project memory: "You can keep a local copy of this wiki or just append useful decisions to a my-plugins-log.md in your project. Tell me if you want me to propose text for it."

### Example Starter Prompt Users Can Give Their LLM
(Users can copy this — it encodes the important rules.)

```
You are helping me develop plugins for SRPG Studio. The srpg-studio-wiki folder is available to you.

Hard rules (follow every time):
- For common features, first suggest checking the Fandom User-Made Plugin Links hub (https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links) and the references/user-made-plugins.md page for existing ready-made solutions. Recommend using one (with credit) if it fits well. Only write new custom code from the core wiki patterns if the user confirms no good match or explicitly wants custom.
- Ground all suggestions in this wiki + official base scripts + official plugins + API docs. Never make up patterns.
- Code must be valid for the SRPG Studio JS environment only (var, classic for loops, IIFE, always alias the original method with .call(this, ...), use createObject* factories, guard .custom with typeof). See wiki/es5-compatibility.md.
- Respond in the same language as the user's query (Japanese queries get Japanese explanations and comments in code where appropriate). The wiki files the LLM reads are in English.
- Be patient and use simple language. Give full plugin files with the standard header (description, 使用方法, author, 更新履歴).
- After code, give exact steps for the editor and testing (test play, skip modes, combinations with other plugins).
- For ongoing work, remember the wiki patterns across messages (user will re-provide files as needed).

First, confirm you read wiki/index.md and wiki/AGENTS.md (or the rules above). Then wait for my first request.
```

### What "Sustained / Persistent Use" Looks Like for End Users
- User keeps the wiki/ available in their LLM tool or re-mentions key files.
- No requirement to edit the central wiki or log.md.
- User's LLM can suggest local additions ("Here is text you could save in your-project/wiki-notes.md or append to a copy of log.md for your own reference").
- The central log.md and wiki are updated only by the people maintaining the distributed reference.

Always reinforce: "Using a good existing Fandom plugin is usually better than a new one from the wiki."

(See also the root AGENTS.md for an even thinner version of these instructions.)

## Core Rules (Never Violate)

1. **Sourcing focus**:
   - Prioritize the base overridable script files (the Script folder and its subfolders in a typical SRPG Studio project), the official example plugins, and the engine's API reference documentation (the HTML files).
   - You may also consult the general llm-wiki.md pattern file (at the project root) and this wiki itself for tone, structure, and maintenance conventions.
   - Unofficial/community plugins (User Made Plugins) can be read privately for implementation ideas and to discover additional patterns. A curated, high-level list of popular/recommended ones (with links to download pages, GitHub, or the Fandom User-Made Plugin Links hub, plus short capability summaries and compatibility notes) is now permitted in the wiki (see references/user-made-plugins.md). This makes the wiki more practical for LLM-assisted development of new plugins, without violating portability. Never copy-paste code or low-level internals from unofficial plugins into wiki pages; only high-level "this enables X, see link". Always prefer grounding in official Script + official plugins (folder name varies by project) + API.
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

**Sources:** Script/xxx.js, Script/yyy.js, official plugins (folder name varies by project)/foo.js, api_references/bar.html, ...

Intro paragraph.

## Section

Details + code (ES5) + warnings.

## See Also

- [other page](...)
```

For reference/glossary pages, be explicit that the HTML + Script sources are the ground truth.

## Old Query / Answer Workflow (Mainly for Wiki Maintenance / Curator Sessions)

(For normal plugin development use the "Usage for Plugin Development" section above instead.)

1. Read `wiki/index.md` to find relevant pages.
2. Read the 1-3 most relevant pages fully.
3. If needed, read the cited source files in Script/official plugins (folder name varies by project)/api_references for precise signatures (use read_file with limits or grep).
4. When the request sounds like a common need (range display, new skill types, UI tweaks, gauges, AI improvements, etc.), check references/user-made-plugins.md (or the Fandom User-Made Plugin Links hub it points to) and suggest relevant existing solutions if they fit. Link the hub for the user. Only proceed to custom implementation from core wiki patterns if no good match or the user specifically wants something new/custom.
5. Synthesize an answer that cites the wiki pages + specific source files/lines where possible.
6. If the answer requires a new code pattern not yet in the wiki (and you are in a curator session), also propose (and later perform) the wiki update as part of the same task.
7. Always remind about ES5 rules and the alias discipline.

In curator/maintenance sessions, good answers can compound the wiki by filing synthesized knowledge back as small updates + log entries.

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