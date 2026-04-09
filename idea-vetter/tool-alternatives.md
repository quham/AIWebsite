# Idea Vetter — Tool Alternatives

Each tool used in the Idea Vetter pipeline, with 3 alternatives including a free/cheap option.

---

## 1. Research Engine (Web Search + AI Analysis)

The backbone — searches the web and synthesizes findings.

| Tool | Cost | Why |
|------|------|-----|
| **Perplexity API (Sonar Pro)** ← Used | $5/1K requests (~$0.02-0.05 per search) | Best research quality. Built-in web search, cites sources, deep reasoning. Purpose-built for research. |
| **OpenAI GPT-4o w/ Web Search** | Pay-as-you-go (~$0.01-0.03 per query) | Good research, native web search via `web_search_options`. Slightly less thorough citations than Perplexity. |
| **Tavily API + any LLM** | Free tier: 1,000 searches/mo | Dedicated search API — pair with any LLM. More control over search depth. Free tier is generous for personal use. |
| **SearXNG (self-hosted) + Ollama** | Free (self-hosted) | Fully free and private. Run SearXNG for search + Ollama with Llama 3 for analysis. Quality depends on your hardware. |

---

## 2. Analysis / Synthesis Engine

Handles the deeper thinking: moat assessment, risk analysis, final verdict.

| Tool | Cost | Why |
|------|------|-----|
| **Claude API (Sonnet 4.6)** ← Used | ~$3/$15 per 1M tokens (in/out) | Best structured reasoning. Follows complex instructions precisely. Excellent at honest, non-cheerleading analysis. |
| **OpenAI GPT-4o** | ~$2.50/$10 per 1M tokens | Strong alternative. Slightly more verbose but very capable at structured analysis. |
| **Google Gemini 2.5 Pro** | Free tier available, then pay-as-you-go | Massive context window (1M tokens). Free tier is usable for personal validation runs. Good at synthesis. |
| **Ollama + Llama 3.1 70B** | Free (local) | Runs locally if you have 48GB+ RAM or a good GPU. No API costs ever. Quality is ~80% of Claude/GPT-4o for this use case. |

---

## 3. Landing Page Builder (for fake door tests)

Used in the validation week to test demand.

| Tool | Cost | Why |
|------|------|-----|
| **Carrd** ← Recommended | $19/yr (Pro) | Dead simple. One-page sites in minutes. Custom domains. Perfect for fake door tests. |
| **Framer** | Free tier, $5/mo for custom domain | More design flexibility. AI-powered layout. Great for polished landing pages. |
| **Typedream** | Free tier available | No-code, clean templates, fast setup. Free tier works for testing. |
| **HTML + Vercel** | Free | If you can code (you can), a static HTML page deployed to Vercel in 2 minutes. Maximum control, zero cost. |

---

## 4. Email Collection / Waitlist

Captures interest from the fake door test.

| Tool | Cost | Why |
|------|------|-----|
| **Loops** ← Recommended | Free up to 1,000 contacts | Modern, developer-friendly. Built for SaaS. Good waitlist and drip sequences. |
| **Buttondown** | Free up to 100 subscribers | Minimalist, markdown-native. Great for simple waitlist collection. |
| **Tally Forms** | Free (generous) | Beautiful forms, no-code, unlimited submissions on free tier. Embed anywhere. |
| **Google Forms + Google Sheets** | Free | Zero cost, works forever. Not pretty but functional. Use with a Carrd page. |

---

## 5. Customer Interview Scheduling

Books calls with potential customers during validation week.

| Tool | Cost | Why |
|------|------|-----|
| **Cal.com** ← Recommended | Free (self-hosted or free tier) | Open source. Clean UX. Free tier includes all core scheduling features. |
| **Calendly** | Free tier (1 event type) | Industry standard. Everyone knows how to use it. Free tier is enough for validation. |
| **SavvyCal** | $12/mo | Best UX for recipients. Overlay calendars. Worth it if you're doing lots of discovery calls. |
| **Google Calendar Appointment Slots** | Free | Built into Google Calendar. No extra tool needed. Slightly clunky but free. |

---

## 6. Outreach / Cold Email

Reaching potential customers and co-founders.

| Tool | Cost | Why |
|------|------|-----|
| **Apollo.io** ← Recommended | Free tier: 50 emails/day | Massive B2B database. Find anyone's email. Built-in sequences. Free tier is enough for validation. |
| **Instantly.ai** | $30/mo | High deliverability, warmup included. Better for volume outreach after initial validation. |
| **Hunter.io** | Free: 25 searches/mo | Find emails from domains. Simple and reliable. Pair with Gmail for manual outreach. |
| **LinkedIn + manual email** | Free | For validation, 20-30 manual LinkedIn messages is often enough. No tool needed. |

---

## 7. Competitor Intelligence

Deep competitor analysis beyond what the AI research provides.

| Tool | Cost | Why |
|------|------|-----|
| **SimilarWeb** ← Recommended | Free tier available | Traffic estimates, top pages, referral sources. Free tier gives enough for validation. |
| **BuiltWith** | Free lookups | See what tech competitors use. Reveals their stack, analytics, and tools. |
| **G2 / Capterra** | Free | Real user reviews of competitors. Best source for what customers hate about incumbents. |
| **Archive.org Wayback Machine** | Free | See how competitors evolved over time. Reveals pivots, pricing changes, messaging evolution. |

---

## 8. Market Size Data

Cross-referencing market numbers from the AI research.

| Tool | Cost | Why |
|------|------|-----|
| **Statista** ← Recommended | Free (limited) / $59/mo | Large database of market stats. Free tier gives headlines. Paid tier for full reports. |
| **IBISWorld** | Paid (university access often free) | Detailed industry reports. Check if you have university/library access. |
| **Google Trends + Census data** | Free | Trend data + government statistics. Manual but free and reliable. |
| **Crunchbase** | Free tier | Funding data, competitor funding rounds, market activity. Free tier is useful. |

---

## Recommended Stack for £0 budget

If you want to run this for free:

1. **Research**: Tavily free tier (1K searches) + Google Gemini free tier
2. **Landing page**: HTML + Vercel (free)
3. **Email capture**: Tally Forms (free) or Google Forms
4. **Scheduling**: Cal.com free tier
5. **Outreach**: LinkedIn manual + Hunter.io free tier
6. **Competitor intel**: G2 + SimilarWeb free + BuiltWith

Total cost: **£0**

## Recommended Stack for best results

1. **Research**: Perplexity Sonar Pro ($5 gets you ~100 research queries)
2. **Analysis**: Claude Sonnet 4.6 (~$0.50 per full validation report)
3. **Landing page**: Carrd ($19/yr)
4. **Email capture**: Loops (free up to 1K)
5. **Scheduling**: Cal.com (free)
6. **Outreach**: Apollo.io free tier
7. **Competitor intel**: SimilarWeb + G2

Total cost: **~$25 for the first year** (most of that is Carrd)
