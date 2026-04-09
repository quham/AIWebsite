---
name: prospect-customers
description: Find 10-15 real potential customers with verified emails and evidence they have the pain point your idea solves. Uses Apify scrapers, job post analysis, and social signal mining.
argument-hint: "description of ideal customer + the problem they have"
---

# Prospect Customers

Find 10-15 REAL potential customers for a startup idea -- people and companies with **verified evidence** they have the pain point the idea solves. Not job-title matching. Pain-signal hunting.

## Invocation

```
/prospect-customers "description of ideal customer + the problem they have"
```

Example:
```
/prospect-customers "Mid-size hotels (50-200 rooms) in tourist cities who struggle to manage influencer partnerships manually -- they're spending staff time coordinating free stays with creators instead of using a platform"
```

## How This Works (Overview)

1. **Parse** the input to extract: customer archetype, industry/vertical, company size, geography, and the core pain point.
2. **Discover** candidate companies/people using the right source for the customer type.
3. **Validate pain signals** -- find concrete evidence each candidate actually has the problem.
4. **Enrich** with emails, LinkedIn URLs, company context.
5. **Score and rank** by strength of pain signal.
6. **Output** a table the founder can act on in under 5 minutes per row.

---

## Step 1: Parse the Input

Extract these fields from the user's description:

| Field | What to capture |
|---|---|
| **Customer Archetype** | The specific job title or role of the person who would buy/use the product |
| **Industry/Vertical** | The sector and sub-segment they operate in |
| **Company Size** | Revenue range, employee count, or other sizing metric relevant to the vertical |
| **Geography** | Target regions, countries, or city types |
| **Core Pain Point** | The primary problem the idea solves — what they're doing manually or poorly today |
| **Adjacent Pain Signals** | Observable behaviors that indicate they have the pain: job posts, social complaints, competitor tool usage, budget allocation patterns |

If the user's description is vague, infer reasonable defaults but call out assumptions.

---

## Step 2: Discovery -- Find Candidate Companies/People

The source depends on customer type. Think: "Where does THIS customer live online?"

### Discovery Source Matrix

| Customer Type | Discovery Source | Apify Actor | Approx Cost | What You Get |
|---|---|---|---|---|
| **SMB/local businesses** (hotels, restaurants, clinics, agencies) | Google Maps | `compass/crawler-google-places` | ~$5-10 per 1K results | Name, address, phone, website, rating, review count |
| **B2B SaaS / tech buyers** | LinkedIn search | `harvestapi/linkedin-profile-search` | ~$4 per 1K (short mode) | Name, title, company, LinkedIn URL |
| **Startup founders** | Crunchbase + LinkedIn | `epctex/crunchbase-scraper` then LinkedIn pipeline | ~$5-8 per batch | Company, funding stage, founders, industry |
| **DTC / ecommerce brands** | Product Hunt + Google Maps | `drobnikj/product-hunt-scraper` + `compass/crawler-google-places` | ~$5-10 | Brand, website, category |
| **YouTube creators (as customers)** | YouTube Channel Search | `streamers/youtube-channel-scraper` | ~$3-5 per 1K | Channel name, subscriber count, email (in description ~50-60% of the time) |
| **Enterprise / Fortune 1000** | LinkedIn + Crunchbase | LinkedIn pipeline + `epctex/crunchbase-scraper` | ~$8-12 | Decision-maker names, titles, company details |

### Discovery Execution

Launch a discovery agent that:

1. **Builds the search query** from the parsed input.
   - For Google Maps: `"boutique hotel" + city name` for each target city.
   - For LinkedIn: job title keywords + industry filters + geography.
   - For Crunchbase: industry category + funding stage + employee count range.

2. **Runs the appropriate Apify actor** via the Apify API.

   ```
   POST https://api.apify.com/v2/acts/{actorId}/runs
   Headers: Authorization: Bearer {APIFY_TOKEN}
   Body: { actor-specific input config }
   ```

3. **Collects 30-50 raw candidates** (we will filter down to 10-15 after pain validation).

#### Apify Actor Configurations

**Google Maps (SMB/local):**
```json
{
  "searchStringsArray": ["boutique hotel", "independent hotel"],
  "locationQuery": "Miami, FL",
  "maxCrawledPlacesPerSearch": 30,
  "language": "en",
  "includeWebResults": false
}
```
Actor: `compass/crawler-google-places`

**LinkedIn Profile Search (B2B):**
```json
{
  "searchTerms": ["hotel marketing manager influencer"],
  "location": "United States",
  "resultType": "people",
  "maxResults": 50,
  "mode": "short"
}
```
Actor: `harvestapi/linkedin-profile-search`

**Crunchbase (Startups):**
```json
{
  "searchQuery": "hospitality technology",
  "fundingRounds": ["Series A", "Series B"],
  "employeeCount": "11-50",
  "maxResults": 30
}
```
Actor: `epctex/crunchbase-scraper`

---

## Step 3: Pain Signal Validation (The Key Differentiator)

This is what separates this skill from generic lead gen. For each of the 30-50 raw candidates, look for CONCRETE EVIDENCE they have the problem.

### Signal Type 1: Job Postings

**Why:** If a company is hiring for a role related to the pain point, they are actively spending money on the problem. This is the strongest buying signal.

**How:**
- Use WebSearch: `site:linkedin.com/jobs "[Company Name]" "influencer" OR "creator partnerships" OR "social media coordinator"`
- Also search: `site:indeed.com "[Company Name]" "influencer marketing"`
- Also search: `"[Company Name]" careers "influencer" OR "creator"`
- Check their website's careers page directly.

**What counts as a hit:**
- Job post for a role that would be eliminated or reduced by your product.
- Job post mentioning tools/processes your product replaces.
- Job post describing the pain point in the responsibilities section.

**Scoring:** Job post found = HIGH pain signal. Recent post (< 30 days) = VERY HIGH.

### Signal Type 2: Social Media Complaints/Discussions

**Why:** People who publicly discuss or complain about a problem are self-qualifying as prospects.

**How (use methods that actually work — not all platforms are Google-indexed):**
- **Reddit** (fully indexed): `site:reddit.com "[Company Name]" OR "[Industry]" "[pain keyword]" "tool" OR "platform" OR "help"`
- **General web mentions** (catches articles, podcast quotes, cross-posted content): `"[Person Name]" "[pain keyword]" "hard" OR "difficult" OR "manual" OR "nightmare"` — no site: filter, cast a wide net
- **Twitter/X** (NOT indexed by Google — use Apify Twitter Scraper directly to search by keyword/hashtag if Twitter signals matter for this customer type)
- **LinkedIn posts** (poorly indexed — only ~10-20% of posts appear in Google): don't rely on `site:linkedin.com/posts`. Instead search broadly: `"[Person Name]" "[topic]"` to find talks, articles, press quotes where they discuss the problem
- **G2/Capterra** (fully indexed): `site:g2.com "[competitor name]"` or `site:capterra.com "[competitor name]"` to find their reviews on competing products

**What counts as a hit:**
- Direct complaint about the problem.
- Post asking for recommendations for solutions.
- Comment on a competitor's post (shows awareness of the space).

**Scoring:** Direct complaint = HIGH. Asking for recs = VERY HIGH. Engaging with competitor content = MEDIUM.

### Signal Type 3: Competitor Usage / Reviews

**Why:** If they are already using (or have reviewed) a competitor, they have acknowledged the problem exists and are willing to pay for solutions.

**How:**
- Use WebSearch: `site:g2.com "[Company Name]" OR "[Person Name]" review "[Competitor Category]"`
- Search Capterra: `site:capterra.com "[Company Name]" review`
- Search Trustpilot: `site:trustpilot.com "[Competitor Name]" review "[Company Name]"`
- Search Product Hunt: `site:producthunt.com "[Competitor Name]" comment "[Person Name]"`

**What counts as a hit:**
- Negative review of a competitor = VERY HIGH (they are unhappy and looking).
- Positive review of a competitor = MEDIUM (they have budget but may be locked in).
- Any review = they acknowledge the problem category.

**Scoring:** Negative competitor review = VERY HIGH. Any competitor review = MEDIUM. On competitor's customer list = LOW-MEDIUM.

### Signal Type 4: Tech Stack Signals

**Why:** Their current tech stack reveals what problems they are solving (or failing to solve) with existing tools.

**How:**
- Check job postings for tool mentions (e.g., "Experience with Hootsuite, Sprout Social" = they manage social but may lack influencer-specific tools).
- Use WebSearch: `site:builtwith.com "[Company Domain]"` to see their marketing stack.
- Check for integrations: if they use tool X, they probably need tool Y.

**What counts as a hit:**
- Using adjacent tools but NOT a direct competitor = HIGH (they have the workflow but a gap).
- Using an outdated or manual version of what you offer = VERY HIGH.
- Using a direct competitor = MEDIUM (potential switch opportunity).

### Signal Type 5: Growth / Funding / Timing Signals

**Why:** Companies that recently raised money, expanded, or hit milestones have both the budget and the urgency to solve problems.

**How:**
- Use WebSearch: `"[Company Name]" raised OR funding OR series OR expansion 2025 OR 2026`
- Check Crunchbase data from the discovery phase.
- Look for recent press coverage about growth.

**What counts as a hit:**
- Raised funding in last 6 months = HIGH (they have budget).
- Expanding to new markets = HIGH (scaling creates the pain).
- Hiring spree = MEDIUM (growing team = growing problems).

### Pain Signal Validation Execution

Run these searches IN PARALLEL across multiple agents:

- **Agent A (Job Posts):** For each of the top 30 candidates, run 1-2 WebSearches for job postings.
- **Agent B (Social/Reviews):** For each candidate, search for social complaints and competitor reviews.
- **Agent C (Tech Stack/Growth):** For each candidate, check BuiltWith and recent news.

After all agents complete, **score each candidate**:

| Score | Criteria |
|---|---|
| 5 - Burning Hair | Multiple strong signals (job post + social complaint + competitor review) |
| 4 - Active Pain | At least one strong signal (job post OR social complaint) |
| 3 - Likely Pain | Indirect signals (competitor usage, adjacent tech stack, industry fit) |
| 2 - Possible Pain | Industry/vertical match only, no specific evidence |
| 1 - Cold | No evidence beyond demographic fit |

**Keep only candidates scoring 3 or above.** Aim for 10-15 final prospects.

---

## Step 4: Enrichment -- Emails, Profiles, Context

### Email Discovery Pipeline

| Customer Type | Email Source | Apify Actor | Notes |
|---|---|---|---|
| **Any (from LinkedIn)** | LinkedIn email finder | `snipercoder/bulk-linkedin-email-finder` | ~$0.60/1K. Feed LinkedIn profile URLs from discovery. |
| **SMB (from website)** | Hunter.io domain search | Use WebSearch: `site:hunter.io "[domain]"` or Hunter API | Free tier: 25 searches/month. Paid: $49/mo for 500. |
| **SMB (from Google Maps)** | Scrape contact page | `apify/website-content-crawler` on their /contact page | Fallback if Hunter.io has no results. |
| **YouTube creators** | Channel description | Already in YouTube scraper output | ~50-60% have email in description. |
| **Catch-all** | Pattern guess + verify | Common patterns: first@domain, first.last@domain | Verify with WebSearch or email verification API. |

### LinkedIn Profile Enrichment

For B2B prospects, get full LinkedIn profiles:

Actor: `supreme_coder/linkedin-profile-scraper`
Cost: ~$3 per 1K profiles

```json
{
  "profileUrls": ["https://linkedin.com/in/person1", "https://linkedin.com/in/person2"],
  "proxyConfiguration": { "useApifyProxy": true }
}
```

This gives: full work history, education, skills, recent posts, connections count.

### Company Context Enrichment

For each company, use WebSearch to gather:
- What the company does (one sentence).
- Employee count / revenue range.
- Recent news (funding, expansion, product launches).
- Key competitors they face.

---

## Step 5: Compile Final Output

### Output Table Format

For each of the 10-15 validated prospects, produce this row:

| Field | Description | Example |
|---|---|---|
| **Full Name** | Real name of the decision-maker | Maria Santos |
| **Title** | Current job title | Director of Marketing |
| **Company** | Company name + size indicator | The Setai Miami (luxury hotel, ~200 rooms, 150 staff) |
| **Location** | City, Country | Miami, FL, USA |
| **Email** | Verified business email | maria.santos@thesetai.com |
| **LinkedIn/Profile URL** | Direct link to their profile | https://linkedin.com/in/mariasantos |
| **Pain Signal** | Specific, concrete evidence they have the problem | Posted LinkedIn job for "Influencer Marketing Coordinator" on March 15, 2026. Job description mentions "managing 50+ creator relationships via spreadsheets" and "coordinating complimentary stays." |
| **Company Context** | Brief on what the company does and why they are a fit | Luxury beachfront hotel in Miami. 4.7 stars on Google (2,800 reviews). Active Instagram presence (45K followers). Recently renovated rooftop bar -- likely seeking influencer coverage for relaunch. |
| **Pain Score** | 1-5 score from validation | 5 |
| **Opening Angle** | Personalized outreach angle based on the pain signal | "Saw you're hiring an Influencer Marketing Coordinator -- what if you could manage all 50+ creator relationships from one dashboard instead of adding headcount? We built exactly that for hotels like The Setai." |

### Output Delivery

Present results in TWO formats:

1. **Summary table** -- one row per prospect, all fields, sorted by Pain Score descending.
2. **Deep dive cards** -- for the top 5 prospects (highest pain scores), provide an expanded card with:
   - Full pain signal evidence (links to job posts, screenshots of complaints, review excerpts).
   - 2-3 personalized outreach angles (email subject lines + opening lines).
   - Suggested follow-up timing (e.g., "Their job post is 5 days old -- reach out this week before they hire").
   - Potential objections and how to preempt them.

---

## Execution Plan (Parallel Agents)

```
User Input
    |
    v
[PARSE] Extract customer archetype, vertical, geography, pain point
    |
    +---> [AGENT A: DISCOVERY]
    |         |
    |         +-- Select correct Apify actor for customer type
    |         +-- Build search queries
    |         +-- Run Apify actor (30-50 raw candidates)
    |         +-- Return raw candidate list
    |
    +---> [AGENT B: PAIN SIGNAL MINING] (starts as soon as Agent A returns first batch)
    |         |
    |         +-- For each candidate (in parallel, batches of 5):
    |         |     +-- WebSearch for job postings at their company
    |         |     +-- WebSearch for social media complaints/posts
    |         |     +-- WebSearch for competitor reviews (G2, Capterra)
    |         |     +-- WebSearch for tech stack (BuiltWith, job post tool mentions)
    |         |     +-- WebSearch for funding/growth news
    |         +-- Score each candidate 1-5
    |         +-- Filter to score >= 3
    |         +-- Return validated candidate list (target: 10-15)
    |
    +---> [AGENT C: ENRICHMENT] (starts as soon as Agent B returns validated list)
              |
              +-- Run email finder (Apify bulk-linkedin-email-finder or Hunter.io)
              +-- Run LinkedIn profile enrichment if B2B
              +-- WebSearch for company context on each
              +-- Craft personalized opening angles
              +-- Return enriched prospect list
    |
    v
[COMPILE] Merge all data into final output table + deep dive cards
```

### Parallel Execution Notes

- Agents B and C can overlap: start enriching candidates as soon as they pass pain validation, do not wait for all candidates to be scored.
- Within Agent B, run pain signal searches for multiple candidates simultaneously (batch of 5 WebSearches at a time).
- Total expected WebSearches: ~100-150 (3-4 searches per candidate x 30-50 candidates).
- Total expected Apify runs: 2-4 (1 discovery + 1-2 enrichment + optional tech stack).

---

## API Configuration

### Required: Apify Token

Store as environment variable `APIFY_TOKEN` or pass directly.

All Apify actors are called via:
```
POST https://api.apify.com/v2/acts/{actorId}/runs
Authorization: Bearer {APIFY_TOKEN}
Content-Type: application/json
```

Wait for run to complete:
```
GET https://api.apify.com/v2/actor-runs/{runId}
```

Get results:
```
GET https://api.apify.com/v2/actor-runs/{runId}/dataset/items
```

### No LinkedIn Login Required

All LinkedIn data comes through Apify actors that handle their own proxy rotation. No cookies or session tokens needed.

### Cost Estimate Per Run

| Component | Est. Cost |
|---|---|
| Discovery (1 Apify actor) | $3-10 |
| LinkedIn email finder | $0.50-2 |
| LinkedIn profile enrichment | $1-3 |
| WebSearch (built into Claude) | $0 |
| **Total per batch of 10-15 prospects** | **$5-15** |

---

## Edge Cases and Fallbacks

| Scenario | Fallback |
|---|---|
| Apify actor fails or times out | Use WebSearch to manually find companies (Google search with site: filters) |
| No email found via LinkedIn finder | Try Hunter.io domain search, then check company website contact page, then pattern-guess (first.last@domain.com) |
| No pain signals found for a candidate | Drop them below the cut line; do not include weak prospects just to fill the list |
| User's customer type does not match any row in the matrix | Default to LinkedIn search (B2B) or Google Maps (local) + WebSearch for everything else |
| Fewer than 10 validated prospects | Expand geography, broaden search terms, or lower pain score threshold to 2 with a caveat |
| User provides only a vague idea | Ask clarifying questions: Who exactly buys this? What size company? What geography? What is the specific pain? |

---

## Quality Checklist (Before Delivering Results)

- [ ] Every prospect has a REAL name (not a generic title like "Marketing Manager at Hotel X").
- [ ] Every prospect has an email address (or a clear note that email was not findable + alternative contact method).
- [ ] Every prospect has at least ONE specific pain signal (not just "they're in the right industry").
- [ ] Pain signals include links or references to the actual evidence (job post URL, social post excerpt, review quote).
- [ ] Opening angles are personalized to the specific pain signal -- not generic templates.
- [ ] Company context is accurate and current (check for recent closures, acquisitions, pivots).
- [ ] No duplicate companies or people in the list.
- [ ] Results are sorted by Pain Score, highest first.
- [ ] Top 5 prospects have deep dive cards with multiple outreach angles.
- [ ] Total cost of Apify runs is reported at the end.
