# AGENTS.md — SRPG Studio Plugin Development with this wiki (for LLMs)

**If you are an LLM (Claude, Grok, Codex, etc.) helping a user create or modify plugins for SRPG Studio:**

This wiki (srpg-studio-wiki/wiki/) is a **portable reference** for correct, compatible plugin development. It is designed especially for users who are SRPG Studio developers but may have limited programming experience or LLM experience.

The wiki focuses on **official patterns** from the base scripts, official example plugins (srpgs_official_plugin), and API docs. It includes a curated high-level list of community plugins (see references/user-made-plugins.md) so LLMs don't have to rediscover everything.

## Guidance for Feature Requests

For common or popular features (UI tweaks, range/AI displays, new skill/item behaviors, gauges, custom windows, etc.):

- Suggest that the user first checks the Fandom User-Made Plugin Links hub (https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links, also summarized at wiki/references/user-made-plugins.md).
- Help the user search the hub for existing ready-made solutions. Recommend downloading, testing, and crediting an existing plugin when it fits (lowest effort and risk).
- Only generate new custom code from the patterns in this wiki if the user confirms no good existing match or explicitly wants a custom implementation.

When you do implement custom code:
- Ground **everything** in this wiki + the official base scripts + official plugins + API reference HTMLs.
- Always follow the environment compatibility rules (see wiki/es5-compatibility.md), alias the original method, use createObject / create*Object factories, guard .custom with typeof, respect skip modes.
- Prefer data-driven approaches (custom parameters + keywords) over hard-coded values.
- Cite specific wiki pages and source files (base scripts or official plugin examples) in your explanations.
- Provide complete, ready-to-use plugin code in the standard official-style header + IIFE.
- Remind the user to test thoroughly (test play, combinations with other plugins, skip modes, save/load, etc.).

Respond in the language of the user's query.

3. Sustained / multi-session development:
   - The user will provide the wiki files (via upload, folder path in their LLM tool, RAG, or project context) in each relevant chat.
   - You can suggest: "For future chats, keep the wiki/ folder available to me."
   - Local notes: If the user wants personal history, they (or you) can append to a *local copy* of wiki/log.md or create a "my-project-notes.md" next to their plugins. The central log.md in this distributed wiki is only updated by maintainers for the reference itself. Do not force the user to maintain the wiki structure.
   - Propose improvements to the wiki only as optional "here is text you could add to your local copy if you want".

## Recommended Starter Prompt (user can copy-paste to their LLM)
```
You are an expert helper for SRPG Studio plugin development.

I have the srpg-studio-wiki (this wiki/) available to you. Use it as the main reference for patterns.

**Guidance for every task:**
- For common features, first suggest checking the Fandom User-Made Plugin Links hub (https://srpg-studio.fandom.com/wiki/User-Made_Plugin_Links) and wiki/references/user-made-plugins.md for existing ready-made solutions. Recommend using one (with credit) when it fits. Only write new custom code from wiki patterns if I confirm no good match or I want something custom.
- Use this wiki + official mechanisms only (base scripts, official plugins, API docs). Never invent patterns.
- All code must follow the SRPG Studio JS environment rules (var, classic for, IIFE, alias the original, etc.). See wiki/es5-compatibility.md.
- Always alias when overriding. Use createObject where appropriate. Guard custom parameters.
- Respond in the same language as my query.
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
- references/user-made-plugins.md (community solutions — link the Fandom hub when relevant for common features)
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
- references/user-made-plugins.md (always prefer linking existing)
- The Fandom hub (primary discovery for ready-made solutions)

If the user is also maintaining their own copy of this wiki, refer to the maintenance parts of LLM-INSTRUCTIONS.md.
