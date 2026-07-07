# Integrations: Google Maps Lead Hunter

| Integration | Required? | Purpose | Access / cost |
|---|---|---|---|
| Outscraper API | Required | Google Maps business search + reviews data | REST + key in Secrets as OUTSCRAPER_API_KEY; free tier: 500 records/mo, then ~$3/1,000 businesses, ~$3/1,000 reviews |
| Google Sheets | Required (option A) | Output table "Google Maps Leads" | OAuth, 1 click |
| Notion | Required (option B) | Output database "Google Maps Leads" | OAuth, 1 click |
| Telegram (channel) | Recommended | Search result summaries, progress updates | 2-min setup |

User chooses Google Sheets OR Notion at onboarding step 4. Both are native OAuth integrations.

Onboarding connection order: Output destination (Sheets or Notion) → Outscraper API key → Telegram.

Degradation: without Telegram — summaries arrive in ASCN chat. Without Outscraper — the agent cannot function (core dependency); onboarding blocks until the key is provided. If the user connects both Sheets and Notion — the agent asks which to use as primary and stores the choice.

Deliberately absent:
- Google Places API (official) — more expensive ($35-40/1,000 vs $3/1,000) and limited to 5 reviews per business. Outscraper offers better value for this use case.
- Apify Google Maps Scraper — good alternative but more complex pricing (per-event + CU). Can be added later if Outscraper doesn't meet needs.
- Email enrichment (Hunter, Apollo) — deliberately out of scope. The agent collects Google Maps data only. Email enrichment is a separate agent's job.
- CRM integrations (HubSpot, Pipedrive) — Sheets/Notion export covers most needs. Custom MCP path available for direct CRM integration if the user requests it later.
