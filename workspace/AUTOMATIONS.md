# Automations: Google Maps Lead Hunter

## Cron tasks

None by default. This is a Project-archetype agent — searches run on the user's command, not on a schedule.

### Optional: Periodic re-scan (offered, not imposed)
If the user wants recurring searches (e.g., weekly new listings check):

- Cron: `0 3 * * 1` (Monday 03:00 — night shift; results delivered after 08:00).
- Task prompt: "Read knowledge/SEARCH_CRITERIA.md for default parameters. Run skill maps-lead-search with those defaults. Deduplicate against existing rows in 'Google Maps Leads'. If auto_review is enabled, run review-analyzer on new leads. Send morning summary to the notification channel after 08:00: new leads count, link to table, API credits used."
- Channel: Telegram / ASCN chat (user's preference from onboarding).
- Credits note: each weekly run uses Outscraper credits proportional to the result count. The user owns this tradeoff.

This cron is NOT created during onboarding by default. The agent mentions it at the end: "Want me to search automatically every week? I can set up a Monday night scan." Create only if the user explicitly agrees.

## No platform triggers
Reactivity is not needed for this agent (it is command-driven). Do not use the word "trigger" in workspace files.

Notifications respect the user's window (default 08:00–23:00); night runs deliver results in the morning.
