# User-Made / Community Plugins (Curated Links & Summaries)

**Primary community hub (recommended first stop):** https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links  
This Fandom page is the most up-to-date aggregator of user-made plugins by creator, with download links (GitHub, Drive, itch.io, etc.), descriptions, and notes. Always check it first for existing solutions.

**Important disclaimer:** This page provides only high-level pointers and summaries based on publicly visible information (Fandom, GitHub READMEs, creator posts). It is **not** a verified or exhaustive list. Links, terms, compatibility, and availability can change. The wiki does not endorse any specific user-made plugin. Always:
- Verify directly from the creator's latest release and terms.
- Test thoroughly in your own project (especially aliasing, load order, and interactions with other plugins).
- Prefer ready-made plugins when they fit, to avoid duplicating effort and introducing compatibility issues.

**For LLMs:** When a user asks for a feature that sounds common (UI tweaks, range displays, new skill types, AI improvements, gauges, etc.), first suggest checking the Fandom User-Made Plugin Links hub (link above) and any matching official patterns in this wiki. Only propose writing custom code from the core alias/defineObject patterns if the user confirms no suitable existing solution or wants something custom. Re-implementing the *idea* using official hooks is preferred over copying.

**Strict limits for this wiki:**
- High-level "this enables X" + link + publicly noted caveats only.
- **Never** include code, specific internals, or low-level implementation details from user-made plugins.
- Ground all new work in the official base scripts + official plugins + API docs documented in the rest of this wiki.

Community plugins are built on the same alias/defineObject/configure*/pushFlowEntry/custom param system described in the core wiki. Use them for inspiration on what users actually want, then implement cleanly. 

Many popular Japanese contributions are distributed as zips (harder to browse source without downloading). Start from Fandom or community Discord for discovery.

## Why Curated Links Help LLM Development
- LLMs can suggest "use something like Goinza's Combat Art or Claris' distance modifiers" and then implement cleanly using official hooks.
- Avoids reinventing common features (custom repair, range displays, AI tweaks, aesthetics).
- Highlights real compatibility pitfalls observed in the wild.
- Users with different project setups still get value without the wiki assuming they have the exact same unofficial files.

## Notable Public Repositories (High-Level Only)

See the Fandom hub for the most current and complete list with download links. Below are a few well-known public GitHub collections frequently referenced (high-level capabilities only; always verify latest and re-implement using official patterns):

- **Goinza**: https://github.com/Goinza/Plugins-for-SRPG-Studio (companion site https://goinzaplugins.vercel.app/). Combat arts, cover, custom windows, damage formulas, range displays, injury/trait systems, weapon ranks, and many QoL/balance tools. Good English docs and structure examples.

- **RogueClaris**: https://github.com/RogueClaris/SRPGStudioScripts/. Strong on UI/graphics/aesthetics (charchip mods, animations, faces, gauges, colored names) and mechanics like distance accuracy and splash concepts. Many custom-param driven.

- **CeruleanAcorn**: https://github.com/CeruleanAcorn/SRPG-Studio-Plugins. Mechanics tweaks (retaliate, army switch, limited EXP, various strike/penalty/supremacy systems). Notes on supporting plugins and aliasing.

- **Blindcoco**, **2chang**, and others: Various utilities, distance mods, target conditions, etc.

Other frequently mentioned creators include MarkyJoe1990, CrazyLi825, Ephraim225, Katafract, etc. Start from Fandom for organized access.

## Additional Japanese Community Public Repos (Examples)

These are public GitHubs (primarily Japanese) often used in the community. Browse their READMEs and headers for usage ideas. Re-implement concepts using the official alias/defineObject/custom patterns from this wiki. High-level notes only:

- QBE256/ExtraPlugin, RantaroGames/SRPG_Studio, iroha-code/SRPGStudio_Plugin, RYBA549/RYBA_SrpgStudio, CordialBun/srpg-studio-plugin, and others.

Details on specific features change; use Fandom + the repos' own docs for current status. Look for patterns like heavy use of custom parameters, state IDs for configuration, and careful coexistence handling.

## Notes on Japanese Community Releases (Often Zip-Distributed)

Many popular contributions (including works associated with creators like o-to, 名前未定（仮） / namaemitei-style, and others) are primarily shared via Google Drive zips or Ci-en articles rather than public Git with browsable source. The Fandom User-Made Plugin Links page is the best starting point for aggregated links and descriptions even for non-Git releases.

Public descriptions, blog/Ci-en titles, and Fandom summaries often give good high-level overviews of custom parameter usage and capabilities (skill condition extensions, resource gauges, AoE behaviors, UI polish, pseudo-item-skill patterns, etc.). Download zips only if you need the full headers/READMEs for a specific project.

**Caution:** Always re-implement any ideas using the official alias/defineObject/configure* + custom param patterns from the core of this wiki. Never copy code or rely on specific zip-internal filenames.

For public GitHub Japanese repos, examples include collections focused on extra commands, AI tweaks, vision/weather systems, UI customizations, advanced mechanics (duplication, alignment, durability control, effective extensions), and developer utilities. Browse their own READMEs for current details and terms. 

See the Fandom hub for the broadest organized access. Test coexistence carefully — many community plugins override core functions (Calculators, AI scorers, draw methods, etc.) and can clash if alias discipline is not followed.

## Zip-Only and Hard-to-Browse Releases (Summary)

Some widely-used contributions (o-to, 名前未定（仮）/namaemitei-style, and similar) are distributed as Google Drive zips or via Ci-en rather than public Git. The Fandom hub is the best aggregator for links and public descriptions.

Public titles, blog/Ci-en posts, and Fandom summaries provide high-level overviews of capabilities (extensive skill condition systems, gauges, AoE/pseudo-skill behaviors, UI polish, etc.). Download zips only when needed for a project.

**Always** re-implement using this wiki's official alias/defineObject/configure*/custom-param patterns. Never copy code or depend on internal zip filenames.

Test coexistence carefully, especially around Calculators, AI, status/map windows, and battle flows. See Fandom for current terms and any English ports/notes.

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

When updating this page, keep entries minimal, high-level, and heavily caveated. Prioritize the Fandom hub link. Add only well-known public repos with clear disclaimers.