---
name: review-analyzer
description: Analyze Google Maps reviews for leads and find sales angles. Use when the user says "analyze reviews", "find pain points", "what can I sell them", or when auto-review is on after a search.
---

# Review analyzer

Read recent bad reviews for each lead, find patterns, and suggest what to say in sales — based on the user's offer.

## How it works
1. Pick which leads to analyze:
   - "Analyze reviews for the last search" → all new leads
   - User names specific businesses → those only
   - Auto-review is on → runs after every search
   - Max 50 per batch (to control API costs). If more — ask first.

2. For each lead, call Outscraper Reviews API:
   - Endpoint: `GET https://api.outscraper.com/google-maps-reviews`
   - Params: place_id, limit 20 reviews, sort by lowest rating, skip empty reviews
   - Auth: OUTSCRAPER_API_KEY

3. From the reviews, find:
   - How many 1-2 star reviews
   - Top 3 complaint themes (slow service, rude staff, high prices, etc.)
   - 2-3 specific quotes (anonymized — never use reviewer names)
   - Trend: getting worse or better recently?

4. Write a sales angle using knowledge/OFFER_CONTEXT.md:
   - Map complaints to the user's offer
   - Write a 1-2 sentence opener a salesperson could use

5. Update the table:
   - review_insights: "Complaints: booking chaos (8 mentions), rude staff (5). Recent quote: 'waited 40 min for a haircut'. Trend: getting worse."
   - suggested_offer: "Their booking chaos → your scheduling tool. Opener: 'I saw 8 recent reviews about booking problems — we helped a similar salon fix this in 2 weeks.'"
   - status: "new" → "analyzed"

## Example output
```
Reviews analyzed: 47 leads

Top patterns:
1. Long wait times (23 out of 47 businesses)
2. Poor communication (19/47)
3. Pricing complaints (12/47)

Best sales opportunities:
1. Glamour Salon (2.8 stars) — 14 complaints about booking → your scheduling system
2. Style Studio (3.1 stars) — 9 complaints about no-shows → your reminder service

Table updated: [link]
API cost: ~$0.15
```

## Edge cases
- Too few reviews (under 5): skip, write "Not enough reviews to analyze."
- No negative reviews: note "No complaints. High satisfaction." Use generic angle from OFFER_CONTEXT.md.
- API error on one lead: skip it, continue with the rest, report at the end.
- No offer context set: show complaints only, skip sales angles. Say "Tell me what you sell and I'll add personalized openers."
- Foreign language reviews: analyze as-is, note the language.
