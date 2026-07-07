---
name: maps-lead-search
description: Search Google Maps for businesses and collect contacts. Use when the user says "find me", "search for", "collect leads", or gives a niche + location.
---

# Maps lead search

Find businesses on Google Maps matching the user's criteria and save them to the table.

## How it works
1. Parse what the user wants: business type, location, how many (default 100, max 500), rating filter, phone/website requirements.

2. If the location is a whole country — split into cities. Start with priority cities from SEARCH_CRITERIA.md. Report progress as you go.

3. Call Outscraper API:
   - Endpoint: `GET https://api.outscraper.com/google-maps-search`
   - Key params: `query` = "[type], [city]", `limit` = how many needed
   - Auth: OUTSCRAPER_API_KEY from Secrets

4. From each result, take: business_name, category, address, city, phone, website, google_maps_link (from place_id), rating, review_count, description.

5. Filter: apply rating threshold, phone/website requirements, remove duplicates against existing table rows, skip closed businesses.

6. Write new rows to "Google Maps Leads" table (Sheets or Notion — whatever the user connected). Columns: business_name, category, address, city, phone, website, google_maps_link, rating, review_count, description, review_insights (empty), suggested_offer (empty), status = "new".

7. Send summary: how many found, how many passed filters, how many new, link to table.

## Example output
```
Search done: "beauty salons" in London, UK
- Found: 127
- After filters (rating < 4.0, has phone): 83
- New (not in table yet): 79
- Saved to: [table link]

Top categories: Hair Salon (34), Nail Salon (22), Beauty Salon (18)
Average rating: 3.4
```

## Edge cases
- Too few results: say honestly "Only 43 found in Leeds. Try raising the rating limit or adding nearby cities."
- API error: retry once, then report what happened and save partial results.
- Same search twice: warn "You searched this on [date]. [N] leads already there. Search again for new ones?"
- Missing phone/website: include the lead, leave field empty, note in summary "23 of 79 have no phone on Google Maps."
