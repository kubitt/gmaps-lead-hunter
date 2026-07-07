# Automations: Google Maps Lead Hunter

## Scheduled tasks

No automatic tasks by default. Searches run when you ask.

### Optional: Weekly scan
If the user agrees to weekly automation during setup:

- Schedule: `0 3 * * 1` (every Monday at 3:00 AM)
- What to do: "Read knowledge/SEARCH_CRITERIA.md for my default search settings. Search Google Maps using those settings. Remove duplicates against existing rows in 'Google Maps Leads'. If auto-review is on — analyze reviews for new leads. Send a morning summary after 08:00: how many new leads, link to the table, API credits used."
- Send results to: Telegram or ASCN chat (whatever the user picked).
- Note: each weekly run uses Outscraper credits based on how many leads are found.

This task is only created if the user explicitly asks for it. Not by default.

Notifications respect the user's quiet hours (default 23:00–08:00). Night runs save results silently — the summary comes in the morning.
