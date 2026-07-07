---
name: review-analyzer
description: Analyze Google Maps reviews for collected leads and generate sales insights. Use when the user says "analyze reviews", "find pain points", "what can I sell them", or when auto-review is enabled and a new search completes.
---

# Review analyzer

Goal: for each lead, fetch recent negative/mixed reviews, identify concrete pain points, and suggest a sales angle tied to the user's offer — so a salesperson can open a conversation with something specific, not generic.

## Algorithm
1. Determine which leads to analyze:
   - If the user specifies: "analyze reviews for the last search" → all leads from the most recent search (status = "new")
   - If the user names specific businesses → those rows only
   - If auto-review is enabled (memory `auto_review=true`) → runs automatically after maps-lead-search completes, for all new leads
   - Max per batch: 50 leads (API cost control). If more — ask: "50 leads will cost ~$X in review credits. Analyze all [N], or just the top 50 by lowest rating?"

2. For each lead, call Outscraper Reviews API:
   - Endpoint: `GET https://api.outscraper.com/google-maps-reviews`
   - Parameters: `query` = place_id or google_id, `reviewsLimit` = 20, `sort` = "lowest_rating", `ignoreEmpty` = true
   - Auth: `OUTSCRAPER_API_KEY`

3. From the fetched reviews, extract:
   - Total negative reviews (1-2 stars) count
   - Top 3 recurring complaint themes (group by topic: service quality, wait times, cleanliness, pricing, communication, professionalism, etc.)
   - 2-3 most specific/actionable quotes (anonymized: "A customer wrote: 'waited 40 minutes for a simple haircut'" — never use reviewer names)
   - Recent trend: are complaints getting worse or better in the last 3 months?

4. Generate the sales insight based on knowledge/OFFER_CONTEXT.md:
   - Map each complaint theme to the user's offer: "They have [complaint] → your [service/product] solves this because [bridge]"
   - Write a 1-2 sentence suggested opener for a salesperson: specific, referencing the real complaint, not generic

5. Update the leads table:
   - review_insights column: "Top complaints: [theme1] (N mentions), [theme2] (M mentions). Recent: [quote]. Trend: [worsening/stable/improving]."
   - suggested_offer column: "Their [pain] → your [solution]. Opener: '[concrete sentence]'."
   - status: "new" → "analyzed"

6. Send summary to notification channel.

## Output
```
Review analysis complete: 47 leads analyzed

Pain point patterns across all leads:
1. Long wait times (mentioned in 23/47 businesses)
2. Rude staff / poor communication (19/47)
3. Pricing complaints (12/47)

Top 5 leads with strongest sales angles:
1. Glamour Salon (rating 2.8) — 14 complaints about booking chaos → your scheduling system
2. Style Studio (rating 3.1) — 9 complaints about no-shows → your reminder service
...

Updated table: [link] — review_insights and suggested_offer columns filled.
API cost this run: ~$0.15 (940 reviews fetched)
```

## Edge cases
- Business has very few reviews (< 5) → skip review analysis, note: "Too few reviews for meaningful analysis." Leave review_insights as "Insufficient reviews (N total)."
- Business has no negative reviews → note: "No complaints found. High satisfaction (rating X.X, N reviews)." suggested_offer: generic angle from OFFER_CONTEXT.md.
- Outscraper reviews API returns empty or errors → mark lead as "review_fetch_failed", report, continue with next lead. Never block the whole batch on one failure.
- User has no offer context (OFFER_CONTEXT.md is empty/default) → provide complaint summary only, skip suggested_offer column. Say: "Tell me what you sell and I'll generate personalized openers. Use: 'My offer is...'"
- Reviews in a foreign language → analyze as-is (Outscraper returns original language). Note the language in insights: "Reviews mostly in [language]. Key complaints: ..."
