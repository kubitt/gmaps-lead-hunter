# Google Maps Lead Hunter

Search Google Maps by your criteria — niche, location, rating, count — and get a ready contact list with optional review-based sales insights.

## Value & ROI
Manual prospecting on Google Maps: 2-3 minutes per business × 100 leads = 3-5 hours of clicking and copying. A VA or lead-gen agency charges $200-500 per batch. The agent does 100 leads in under 5 minutes, with structured data and deduplication. Total cost: $29/mo (ASCN) + ~$5-15/mo (Outscraper API at typical usage of 500-2,000 leads/mo). Payback vs $29+$15: 5-15x compared to manual work or outsourcing.

## What it does
1. Accepts search criteria in natural language: "Find me 100 beauty salons in London with rating below 4 stars."
2. Searches Google Maps via Outscraper API: collects business name, category, full address, city, phone, website, Google Maps link, rating, review count, description.
3. Filters by your criteria: rating threshold, must-have phone/website, deduplicates against previous searches.
4. Writes results to Google Sheets or Notion (your choice at setup) in the "Google Maps Leads" table.
5. Optional: analyzes recent negative reviews for each lead — identifies complaint patterns (wait times, rude staff, pricing) and generates specific sales angles tied to your offer.
6. Delivers a summary: total found, total after filters, top categories, average rating, link to the table.
7. Handles country-wide searches by splitting into city-level batches with progress updates.
8. Optional weekly re-scan cron: checks for new listings matching your defaults every Monday night.

## Folder contents
- `workspace/` — copied into the ASCN agent workspace (IDENTITY, SOUL, ONBOARDING, 2 skills, 2 knowledge files).
- `DEPLOY_PROMPT.md` — prompt for Claude Code that transfers everything to ASCN.
- `INTEGRATIONS.md`, `AUTOMATIONS.md` — integration reference and optional cron task texts.

## Quick deploy
1. Open Claude Code in this folder.
2. Paste the contents of `DEPLOY_PROMPT.md`.
3. Send the agent "Hi, let's set up" — it runs its own onboarding (~8 min: what you sell, search defaults, API key, output destination, test search on 10 real businesses).

## Required integrations
Native: Google Sheets (option A) or Notion (option B) — user chooses at setup. External: Outscraper API — Google Maps data — free tier 500 records/mo, then ~$3/1,000 (~$5-15/mo typical). Channel: Telegram recommended.

## Deliberate limitations
- **No email/contact enrichment beyond Google Maps data.** Phone and website come from Google Maps listings only. If a business didn't list a phone on Google — the field stays empty. Email lookup (Hunter, Apollo) is a separate service and a separate agent's job.
- **No browser automation.** All data via Outscraper's commercial API, which handles Google Maps compliance.
- **No outreach.** The agent delivers the list — cold emails, calls, or messages are the user's responsibility (or another agent's).
- **Review analysis is approximate.** The agent reads reviews through an LLM — it catches themes and sentiment well, but nuance in sarcasm or local slang may be missed. Review insights are starting points, not verdicts.
- **Google Maps coverage gaps.** Some businesses don't list phone numbers or have outdated info on Google Maps. The agent reports what's there — it never invents or "enriches" missing data from other sources.
- **500 results per search maximum.** Outscraper API limit per query. For larger volumes — run multiple searches with different locations or criteria.
