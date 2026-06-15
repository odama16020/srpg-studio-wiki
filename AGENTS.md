# AGENTS.md — SRPG Studio Plugin Development with this wiki (for LLMs)

**If you are an LLM (Claude, Grok, Codex, etc.) helping a user create or modify plugins for SRPG Studio:**

This wiki (srpg-studio-wiki/wiki/) is a **portable reference** for correct, compatible plugin development. It is designed especially for users who are SRPG Studio developers but may have limited programming experience or LLM experience.

The wiki focuses on **official patterns** from the base scripts, official example plugins (srpgs_official_plugin), and API docs. It includes a curated high-level list of community plugins (see references/user-made-plugins.md) so LLMs don't have to rediscover everything.

## Absolute Priority Rule (Never Skip)
**For EVERY feature request the user makes:**

1. **Fandom First — Do not reinvent the wheel.**
   - Your very first response must include: "Before we write any new code, the best and easiest thing is usually to use an existing community plugin if one already does what you want."
   - Direct the user to the primary hub: https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links
   - Help them search or describe relevant sections (e.g., "Look for creators like Goinza, Claris, o-to, 名前未定（仮）, or keywords like 'splash', 'range display', 'custom item', 'gauge', 'AI'").
   - If a suitable plugin exists: Recommend downloading it, reading its README/usage, testing it in isolation, and giving credit to the author. This is the lowest-effort, lowest-risk path for the user.
   - Only proceed to generating new code if the user explicitly says "no good match exists", "I need a modified/custom version", or "I want to understand how to implement it myself using official methods".

2. When implementing (only after Fandom check):
   - Ground **everything** in this wiki + the official base scripts / srpgs_official_plugin / API reference HTMLs.
   - Always follow ES5 rules (see wiki/es5-compatibility.md), alias the original method, use createObject / create*Object, guard .custom with typeof, respect skip modes.
   - Prefer data-driven (custom parameters + keywords) over hard-coded.
   - Cite specific wiki pages and source files in your explanations.
   - Provide complete, ready-to-use plugin code in the official header + IIFE style.
   - Remind the user to test thoroughly (test play, combine with other plugins, skip modes, save/load, etc.).

3. Sustained / multi-session development:
   - The user will provide the wiki files (via upload, folder path in their LLM tool, RAG, or project context) in each relevant chat.
   - You can suggest: "For future chats, keep the wiki/ folder available to me."
   - Local notes: If the user wants personal history, they (or you) can append to a *local copy* of wiki/log.md or create a "my-project-notes.md" next to their plugins. The central log.md in this distributed wiki is only updated by maintainers for the reference itself. Do not force the user to maintain the wiki structure.
   - Propose improvements to the wiki only as optional "here is text you could add to your local copy if you want".

## Recommended Starter Prompt (user can copy-paste to their LLM)
```
You are an expert helper for SRPG Studio plugin development.

I have the srpg-studio-wiki (this wiki/) available to you. Use it as the main reference for patterns.

**Hard rules for every task:**
- Fandom first: For any requested feature or behavior, immediately tell me to check https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links . Help me find if a ready-made plugin already exists. Recommend downloading and using an existing one (with credit) whenever possible. Only write new code if I confirm no suitable existing plugin.
- Use this wiki + official mechanisms only (base Script, srpgs_official_plugin, API docs). Never invent patterns.
- All code must be valid ES5 (var, classic for, IIFE, alias the original, etc.). See wiki/es5-compatibility.md.
- Always alias when overriding. Use createObject where appropriate. Guard custom parameters.
- Be patient and explain simply. Give complete plugin files with the standard header (description, 使用方法, author, 更新履歴).
- After giving code, give clear installation + testing steps.
- If something is already covered well in the wiki or Fandom, link or quote it instead of repeating.

Start by reading wiki/index.md and wiki/getting-started.md to understand the structure. Then wait for my request.
```

## Quick Workflow for a Typical Session
1. User gives the starter prompt (or similar) + wiki files.
2. You read index.md → identify pages.
3. For the request: Fandom check first (as above).
4. If needed: read 1-3 wiki pages + relevant official patterns.
5. Generate clean code + explanation citing wiki.
6. Give testing advice.
7. (Optional) If user wants, propose text for their local log or notes.

## Key Files to Prioritize
- wiki/index.md (catalog)
- wiki/getting-started.md + es5-compatibility.md + overriding-patterns.md (foundations)
- references/user-made-plugins.md (community solutions — link these heavily)
- Domain pages as needed (calculators, items, AI, battle, events, UI, etc.)
- references/official-plugin-patterns.md (map your goal to official examples)

## Tone for Non-Expert Users
- Use simple Japanese + English as appropriate (user may prefer Japanese).
- Short steps, numbered lists, "copy this code", "do this in the editor".
- Never assume the user knows git, advanced LLM features, or deep JS.
- Celebrate using existing plugins — it's smart, not "giving up".

This AGENTS.md + the wiki makes plugin development accessible with low learning overhead once the wiki files are available to the LLM.

See also:
- wiki/LLM-INSTRUCTIONS.md (full details; focus on the "Query / Answer Workflow" and Fandom rule for plugin dev sessions)
- wiki/user-made-plugins.md (always prefer linking existing)
- The Fandom hub (primary discovery for ready-made solutions)

If the user is also maintaining their own copy of this wiki, refer to the maintenance parts of LLM-INSTRUCTIONS.md.
