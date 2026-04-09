---
name: prospect-influencers
description: Find 10-15 real influencer/creator partners with contact info, deep content analysis, audience-product fit scoring, and partnership readiness signals. Analyzes actual video/post content to evaluate fit beyond follower counts.
argument-hint: "niche + platform preference + follower range (e.g., 'luxury travel TikTok 50K-500K')"
---

# Prospect Influencers

Find 10-15 REAL influencer/creator partners for a startup idea. Given a niche, platform, and follower range, deliver actionable partnership targets with handles, contact info, audience analysis, content evaluation, and evidence of audience-product fit.

## Invocation

```
/prospect-influencers "niche + platform + follower range"
```

Example: `/prospect-influencers "luxury travel TikTok 50K-500K"`

## What Makes This Different

We don't just find people with follower counts. We do DEEP CONTENT ANALYSIS to evaluate actual fit:

### 1. Content Analysis (the key differentiator)

- Scrape their recent posts/videos (titles, descriptions, hashtags, view counts)
- Feed 5-10 of their top-performing videos/posts into analysis to understand: What topics do they cover? What's their tone/style? What audience do they attract?
- Evaluate: does their content ACTUALLY align with the product, or do they just loosely fit the niche?
- Look for content gaps — are they missing something the product could fill?

### 2. Audience Quality Signals

- Engagement rate (likes+comments / followers) — scrape the most recent 22 videos, drop the highest and lowest engagement posts (removes outliers/virals and duds), calculate ER from the remaining 20. Benchmarks: above 3% on TikTok, 1.5% on Instagram is strong.
- Comment quality scan — scrape 20-30 comments from their top videos. Real sentences and questions from followers = genuine engagement. Generic one-word replies, emoji-only, or repetitive comments = bot/low-quality audience. Manual judgment from scraped data.
- Performance trend (proxy for growth) — compare average views/likes on their last 5 videos vs the 5 before that. Rising = growing creator. Declining = stalling. This is the best proxy available — no scraper returns historical follower counts.
- Audience demographics clues — inferred from content language, locations mentioned/tagged, content topics, and comment language. No direct demographic data is available from scrapers.

### 3. Partnership Readiness Signals

- Have they done brand partnerships before? (look for #ad, #sponsored, #partner in recent posts)
- Do they have a business email in bio?
- Do they have a media kit or link in bio to a booking page?
- Response rate signal — do they engage with comments? Active or dormant?

### 4. Audience-Product Fit Scoring

- Does their audience match the product's target user?
- E.g., for a luxury hotel platform: a travel creator showing budget hostels = poor fit even if they have 500K followers. A creator showing boutique hotels to an affluent audience = perfect fit even at 50K.
- Quality of fit > quantity of followers

## Apify Pipeline

### TikTok

| Step | Actor | Cost/1K | Purpose |
|---|---|---|---|
| 1. Discover | `clockworks/tiktok-scraper` | $1.70/1K | Search by hashtag/keyword to find creators. Returns: username, follower count, video stats, bio. 4.7 stars, 155K users. |
| 2. Deep profile | `clockworks/tiktok-profile-scraper` | $2.50/1K | Full profile + recent video data for shortlisted handles. 4.9 stars, 21K users. |

### Instagram

| Step | Actor | Purpose |
|---|---|---|
| 1. Discover | Instagram Scraper (Apify) | Search by hashtag/location |
| 2. Profile | Instagram Profile Scraper | Bio, follower count, recent posts, business email |

### YouTube

| Step | Actor | Purpose |
|---|---|---|
| 1. Discover | YouTube Channel Scraper | Search by keywords/topics |
| 2. Profile | Channel description scrape | Email (~50-60% in description), subscriber count, video stats |

## Content Deep-Dive Process

For each shortlisted creator (top 15-20 from discovery):

1. Scrape their 10 most recent posts/videos (titles, descriptions, hashtags, view counts, engagement)
2. Scrape their 5 highest-performing posts ever (if available)
3. Use WebSearch to find any press/articles about them
4. Analyze the content to determine:
   - Primary content themes (luxury travel, budget tips, food, wellness, adventure, etc.)
   - Content quality and production value
   - Posting frequency and consistency
   - Brand partnership history and how they integrate sponsors
5. Score audience-product fit on a 1-10 scale with specific reasoning

## Platform Selection Logic

| Product Type | Primary Platform | Why |
|---|---|---|
| Visual/lifestyle (hotels, fashion, food) | TikTok + Instagram | Visual discovery platforms |
| Education/how-to (SaaS tools, courses) | YouTube + TikTok | Long-form content drives conversion |
| B2B/professional | LinkedIn + Twitter/X + YouTube | Professional audiences |
| Local/experience-based | TikTok + Instagram | Location-tagged content |
| Tech/developer tools | YouTube + Twitter/X | Dev community lives here |

## Output Format

For each influencer, produce a row with the following fields:

| Field | Description |
|---|---|
| Name / Handle | Real name + platform handle |
| Platform | TikTok / Instagram / YouTube / Multi |
| Followers | Count |
| Engagement Rate | Calculated from recent posts |
| Bio | Full bio text |
| Email / Contact | Business email or contact method |
| Content Themes | Top 3 topics they cover |
| Top Performing Content | 3 best posts with view/like counts |
| Brand Partnership History | Evidence of past #ad/#sponsored + which brands |
| Audience-Product Fit | 1-10 score with specific reasoning |
| Content Style | Tone, production quality, posting frequency |
| Partnership Readiness | Has media kit? Business email? Active? Done collabs? |
| Why Them | 2-3 sentence case for why this creator is a strong partner |
| Opening Angle | Personalized first DM/email angle referencing their specific content |

## Execution

1. **Parse the input** — Extract niche, platform preference, and follower range from the argument string.
2. **Select platforms** — Use the Platform Selection Logic table if no platform is specified.
3. **Launch parallel agents:**
   - Agent 1: Discovery scraping via Apify (hashtag/keyword search on target platforms)
   - Agent 2: Deep profile scraping for shortlisted handles (top 15-20 from discovery)
   - Agent 3: Content analysis + web research (WebSearch for press, articles, brand deals)
4. **Parse emails from bios automatically.**
5. **Score and rank** all candidates by audience-product fit.
6. **Return the top 10-15** with full output format above.

### Requirements

- Only requires Apify API token (set as `APIFY_TOKEN` environment variable)
- No platform logins needed
- The output should be so good that the founder can send a personalized partnership DM within 5 minutes of reading each row, referencing specific videos the creator made
