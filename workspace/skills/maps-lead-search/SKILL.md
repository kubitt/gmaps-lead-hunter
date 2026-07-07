---
name: maps-lead-search
description: Search Google Maps for businesses and collect contacts. Use when the user says "find me", "search for", "collect leads", gives a niche + location, or asks to refine/expand a previous search.
---

# Maps lead search

Goal: deliver a filtered, deduplicated list of businesses from Google Maps with full contact details, written to the user's table — fast and with zero invented data.

## Algorithm
1. Parse the user's request into structured criteria:
   - `query`: business type/niche (e.g., "beauty salons", "dental clinics", "restaurants")
   - `location`: city, region, or country (e.g., "London, UK")
   - `max_results`: target count (default 100, max 500)
   - `rating_filter`: e.g., "below 4.0", "above 3.5", "any" (default: any)
   - `must_have_phone`: true/false (default: false)
   - `must_have_website`: true/false (default: false)
   - Priority cities if the location is a country (user may specify "start with London, then Manchester, Birmingham").

2. If the location is broad (country-level), break it into city-level searches. Start with priority cities, then expand. Report progress: "Searching London... 45 found. Moving to Manchester..."

3. Call Outscraper Google Maps Search API:
   - Endpoint: `GET https://api.outscraper.com/google-maps-search`
   - Parameters: `query` = "[niche], [city]", `limit` = min(500, remaining_needed), `language` = "en"
   - Auth: API key from Settings → Secrets as `OUTSCRAPER_API_KEY`

4. For each result, extract and normalize:
   - business_name (from `name`)
   - category (from `category`)
   - address (from `full_address`)
   - city (from `city`)
   - phone (from `phone` — keep original format with country code)
   - website (from `site`)
   - google_maps_link (construct from `place_id`: `https://www.google.com/maps/place/?q=place_id:{place_id}`)
   - rating (from `rating`)
   - review_count (from `reviews`)
   - description (from `description` or `subtypes` joined)

5. Filter:
   - Apply rating_filter (e.g., keep only rating < 4.0)
   - Apply must_have_phone / must_have_website if set
   - Deduplicate against existing rows in "Google Maps Leads" table by business_name + city + phone
   - Remove results with business_status != OPERATIONAL

6. Write to the user's chosen destination (Google Sheets or Notion — stored in memory as `output_destination`):
   - Table: "Google Maps Leads"
   - Columns: business_name, category, address, city, phone, website, google_maps_link, rating, review_count, description, review_insights (empty — filled by review-analyzer), suggested_offer (empty), status (= "new")
   - Append rows; never overwrite existing data.

7. Send summary to the notification channel: total API results, total after filtering, total new (excluding duplicates), link to the table. If fewer than requested — explain why.

## Output
```
Search complete: "beauty salons" in London, UK
- API returned: 127 businesses
- After filters (rating < 4.0, has phone): 83
- New (not already in table): 79
- Written to: [Google Sheets link]

Top categories: Hair Salon (34), Nail Salon (22), Beauty Salon (18), Spa (5)
Rating range: 2.1 – 3.9 | Average: 3.4
```

## Edge cases
- Location too broad + high count (e.g., "restaurants in UK, 500") → break into top-10 cities by population, search each, merge results. Warn: "UK-wide search will use ~10 API calls across cities. Proceeding."
- Fewer results than requested → report honestly: "Only 43 beauty salons with rating < 3.0 exist in Leeds. Consider raising the rating threshold or expanding to nearby cities."
- Outscraper API error or rate limit → retry once after 30 seconds; if still failing, report the error and partial results. Never silently drop a failed city.
- Duplicate search (same niche + city as a previous run) → warn: "You searched this before on [date]. [N] leads already in the table. Search again for new listings? Or try a different area?"
- Phone/website missing for some results → include them with empty fields; note in the summary: "23 of 79 leads have no phone listed on Google Maps."
