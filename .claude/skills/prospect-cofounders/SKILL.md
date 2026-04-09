---
name: prospect-cofounders
description: Find 10 real GTM co-founder candidates with verified emails, domain expertise evidence, network analysis, and personalized outreach angles. Focused on distribution-focused people under 30.
argument-hint: "vertical/domain of the startup idea"
---

# Prospect Co-Founders

Find real potential GTM co-founders for a startup idea. Invoked as `/prospect-cofounders "domain/vertical of the idea"`.

## Critical Context

The user is a technical cofounder who handles ALL engineering. NEVER suggest technical co-founders. The co-founder needed is ALWAYS a distribution-focused GTM person with:

- Domain expertise in the idea's vertical (or relevant adjacent experience)
- Existing network in the target market (ideally both expertise + network)
- Under 30 years old
- GTM/sales/BD/partnerships background

## Purpose

Given a startup idea and its vertical, find 10 REAL people who could be the GTM co-founder — with names, emails, LinkedIn URLs, and evidence of their domain expertise and network.

## What Makes This Different From Generic Recruiting

We don't just find people with BD titles. We look for DEEP FIT SIGNALS:

### 1. Domain Expertise Signals

- Have they worked at companies in this exact vertical? (e.g., worked at Evolusen, Curacity, BLLA if hospitality)
- Have they posted LinkedIn content about this domain? (scrape their recent posts)
- Have they spoken at industry events?
- Did they study something relevant?

### 2. Network Signals (only use what's actually scrapeable)

- LinkedIn connection count — 500+ suggests strong network (from profile scraper)
- Worked at multiple companies in the target vertical (from job history in profile scraper)
- Published content about the domain — WebSearch `"their name" + domain keywords`
- Spoken at industry events / podcasts — WebSearch `"their name" + "conference" OR "podcast" OR "speaker"`
- Twitter/X presence in the niche — check if they have a handle, scrape if so
- Mutual companies with target customers — cross-reference their past employers with the customer segment

### 3. GTM Capability Signals

- Sales/BD/partnerships track record at relevant companies
- Have they grown something before? (revenue, user base, partnerships)
- Content they've posted about growth, sales, partnerships

### 4. Age/Stage Signals

- Graduation year 2017+ (under 30)
- Early career but ambitious (multiple roles, fast progression, or startup experience)
- Entrepreneurial indicators (side projects, founded something, active on Twitter/LinkedIn about building)

## Apify Pipeline

| Step | Actor | Cost/1K | Purpose |
|---|---|---|---|
| 1. Search | `harvestapi/linkedin-profile-search` | ~$4/1K | Search by GTM titles + industry + location. Example filters: title=["Head of Partnerships", "BD Manager", "Growth Lead"] — **adapt titles and industry to the specific idea/vertical, these are examples not a fixed list.** Use the domain templates below as starting points, then expand or narrow based on the idea. |
| 2. Profile | `supreme_coder/linkedin-profile-scraper` | $3/1K | Full profile: experience, education, skills, about section |
| 3. Email | `snipercoder/bulk-linkedin-email-finder` | $0.60/1K | Verified email |

## Post-Scrape Analysis (The Key Differentiator)

After getting profiles, use WebSearch to deeply evaluate each candidate:

- Search their name + company + domain keywords to find posts, talks, articles
- Search their LinkedIn activity (recent posts about the domain)
- Check if they've posted about wanting to start something or looking for co-founders
- Look at their career trajectory — are they the type to leave for a startup?

## Search Query Templates by Domain (starting points — adapt per idea)

These are examples to get started. Think critically about what titles and companies make sense for the specific idea, then add/remove/modify accordingly.

| Domain | Example Titles (adapt) | Industry ID | Example Company Keywords (adapt) |
|---|---|---|---|
| Hospitality/travel | "Head of Partnerships", "BD Manager", "Marketing Director" | 53 (Hospitality) | hotel tech, OTA, travel platform |
| Creator economy | "Creator Relations", "Head of Partnerships", "BD Lead" | N/A | talent agency, influencer platform, creator |
| E-commerce/DTC | "Head of Growth", "Partnerships Lead", "BD Manager" | 67 (Retail) | Shopify, DTC brand, e-commerce agency |
| B2B SaaS | "AE", "Head of Sales", "Partnerships Manager" | 4 (Software) | adjacent SaaS companies |
| Healthcare | "BD Manager", "Partnerships", "Sales Lead" | 14 (Healthcare) | health tech, compliance |
| Fintech | "BD Manager", "Growth Lead", "Partnerships" | 43 (Financial Services) | fintech, neobank, payments |

## Output Format

For each co-founder candidate, provide:

| Field | Description |
|---|---|
| Full Name | Real name |
| Age (est.) | Based on graduation year and years of experience|
| Current Title | Job title |
| Company | Current company + what it does |
| LinkedIn URL | Direct link |
| Email | Verified |
| Domain Expertise | Specific evidence: worked at [X] in this vertical for [Y] years, posted about [Z] |
| Network Strength | Connection count, relevant companies in their network, industry involvement |
| GTM Track Record | What they've built/sold/grown — specific numbers if findable |
| Entrepreneurial Signal | Side projects, startup experience, "looking to build" posts |
| Why Them | 2-3 sentence pitch for why this person is a strong co-founder match |
| Opening Angle | Personalized first message angle |

## Execution

- Launch agents in parallel: one for LinkedIn search + profile scraping, one for deep web research on each candidate
- The output should be so good that the user can send a co-founder pitch within 5 minutes of reading each row
