# Claude Code prompt: deploy "Google Maps Lead Hunter" to ASCN

Copy the text below into Claude Code opened in this folder (gmaps-lead-hunter/).

---

You are transferring the ready-made agent "Google Maps Lead Hunter" to the ASCN platform (ascn.ai).

Access method (ask me if unclear from the environment):
A) Direct file access to the agent workspace — copy files directly.
B) ASCN workspace MCP (https://api.clawman.ascn.ai/api/v1/mcp, Bearer key from Settings → API) — pass file contents via the `chat` tool asking the agent to save to the given paths.

Do in order:
1. Read every file in this project's workspace/ folder.
2. Transfer into the ASCN agent workspace:
   - workspace/IDENTITY.md → /data/workspace/IDENTITY.md (replace)
   - workspace/SOUL.md → /data/workspace/SOUL.md (replace)
   - workspace/ONBOARDING.md → /data/workspace/ONBOARDING.md (new)
   - workspace/skills/maps-lead-search/SKILL.md → /data/workspace/skills/maps-lead-search/SKILL.md
   - workspace/skills/review-analyzer/SKILL.md → /data/workspace/skills/review-analyzer/SKILL.md
   - workspace/knowledge/SEARCH_CRITERIA.md → /data/workspace/knowledge/SEARCH_CRITERIA.md
   - workspace/knowledge/OFFER_CONTEXT.md → /data/workspace/knowledge/OFFER_CONTEXT.md
   Do NOT touch platform system files (AGENTS.md, MEMORY_POLICY.md, INTEGRATIONS.md, AUTOMATIONS.md, CHANNELS.md, rules/).
3. Secrets before first run: create in Settings → Secrets: OUTSCRAPER_API_KEY (Outscraper API key; the user gets it at outscraper.com → Profile → API).
4. Do NOT create automations yourself: the agent offers a weekly re-scan cron during use, not during onboarding (texts in this folder's AUTOMATIONS.md — verify if you wish).
5. Verify yourself: list transferred files and print the first 3 lines of each from the agent workspace.
6. Remind me: on first dialog the agent will request Google Sheets or Notion via OAuth buttons, ask for the Outscraper API key, and offer Telegram.
7. Send the agent "Hi, let's set up" and confirm it starts onboarding (introduces itself, states ~8 minutes, asks the first question about what you sell).

Ready criterion: files in place; the agent responds with the onboarding scenario, not generic chat.
