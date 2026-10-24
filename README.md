# real estate scraper api: Scrape Zillow & Realtor Data Without Getting Blocked — Credit Costs, Plans, and Which Tier Actually Fits

A real estate scraper API is the practical way to pull listing data from sites like Zillow, Realtor.com, or MLS portals at scale: you send a request, and you get back the property price, address, beds, baths, agent contact info, and listing history as structured data. The catch is that real estate sites are among the hardest to scrape — Zillow loads listings with JavaScript and sits behind serious bot protection, so a plain `requests.get()` gets you an empty shell or a CAPTCHA page. That's why most people end up using a scraping API that handles rendering and anti-bot bypass for them. This article walks through how that works, what ScraperAPI's plans actually cost per request (the pricing page doesn't spell this out), and which tier makes sense depending on how much data you need.

> **Quick verdict:** Most real estate projects — monitoring a few hundred to tens of thousands of listings per month — fit comfortably in the **Startup ($149/mo, 1M credits)** or **Business ($299/mo, 3M credits)** tier. Sign-up comes with 5,000 free credits for a 7-day test, no credit card needed, and there's a 7-day no-questions-asked refund if it doesn't work for your targets. 👉 Check current plan pricing on ScraperAPI

## What a real estate scraper API actually has to handle

Scraping property data is harder than scraping a blog. Three things make it messy:

1. **JavaScript rendering.** On Zillow, the listings you see are injected client-side. The raw HTML returned by a standard request doesn't contain the property cards at all, so any scraper that doesn't render JS comes back with nothing to parse.
2. **Anti-bot systems.** Real estate portals run Cloudflare, Datadome, PerimeterX, and similar protection. Get flagged once and your whole crawl session gets CAPTCHA-walled.
3. **Rate limiting and IP blocks.** Pull the same ZIP code pages every day from one IP and you'll be blocked quickly.

This is the gap a scraping API fills. Instead of managing proxy pools, rotating user agents, and headless browsers yourself, you send your target URL to the API and it returns the finished HTML (or structured data), with rendering and bypassing handled on their end.

ScraperAPI's approach: you hit `https://api.scraperapi.com/` with your target URL and API key, and the service renders the page through a pool of 40M+ IPs (a mix of datacenter, residential, and mobile proxies) before returning the HTML. CAPTCHA handling is automatic — the system retries within 60 seconds and picks proxy and header combinations based on accumulated traffic analysis. Bandwidth is unmetered, with speeds up to 100 Mb/s and a 99.9% uptime guarantee.

For Zillow specifically, the official setup adds a `render=true` flag plus a "render instruction set" — a small JSON script telling the browser to wait for the price selector to appear, scroll to the bottom of the page, then wait a few seconds for everything to load. That mimics how a real person browses, which is what gets the full listing grid back instead of a half-loaded page.

## What you can actually pull from Zillow with it

The output goes beyond the basics. ScraperAPI's Zillow scraper page shows a sample JSON export for a Missouri farmland listing that includes: price ($260,000), full address, beds and baths, lot size (38.07 acres), the Zestimate® estimate, HOA details, annual property tax ($92), listing date, transaction terms (cash or conventional), days on market, view and favorite counts, agent name and phone number, and the MLS number.

That last group — agent contacts and MLS numbers — is what most lead-generation and market-analysis projects are really after, and it's why real estate scraping has become its own product category rather than a footnote in general web scraping.

One feature worth knowing about if you're feeding data into an LLM pipeline: setting the `output` parameter to `text` or `markdown` returns the page in an LLM-ready format, so you can skip writing a parser entirely. The official examples use this for natural-language property search assistants ("homes within 30 minutes of downtown") and automated valuation engines.

If you'd rather not write code at all, the DataPipeline lets you submit a list of Zillow URLs, pick an output format, and schedule recurring runs. For large jobs, the Async Scraper queues millions of requests and returns results via webhook.

## ScraperAPI plans and pricing (full table)

All paid plans include JavaScript rendering, premium residential and mobile IPs, CAPTCHA and anti-bot handling, JSON auto-parsing, unlimited bandwidth, and the 99.9% uptime guarantee. Annual billing takes 10% off the monthly price.

| Plan | Credits/month | Concurrency | Price (monthly / billed annually) | Get started |
| --- | --- | --- | --- | --- |
| Free | 1,000 | 5 | $0 | [ Try the free plan](https://www.scraperapi.com/signup?fp_ref=coupons) |
| Hobby | 100,000 | 20 | $49 / $44.10 | [ Start with Hobby](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149 / $134.10 | [ Start with Startup](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Business | 3,000,000 | 100 | $299 / $269.10 | [ Start with Business](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Scaling | 5,000,000 | 200 | $475 / $427.50 | [ Start with Scaling](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | $975 / $877.50 | [ Start with Professional](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | $1,975 / $1,777.50 | [ Start with Advanced](https://www.scraperapi.com/pricing/?fp_ref=coupons) |
| Enterprise | 22M+ | 500+ | Custom quote | [ Talk to sales about Enterprise](https://www.scraperapi.com/contact-sales/?fp_ref=coupons) |

Geotargeting is included on every plan (50+ locations), but there's a split that matters for real estate work: **Hobby and Startup are limited to US & EU geotargeting only**. If you need listings localized to other countries, you need Business or above. The analytics dashboard is also capped at 30 days of history on Hobby and Startup, unlimited on Business and up.

## The real cost per request — the part most reviews skip

ScraperAPI charges in credits, and the credit price of a request depends on the target domain and your parameters. A standard page costs 1 credit. But real estate scraping rarely stays at 1 credit, because you'll almost always turn on rendering, and often premium IPs too:

| What you add | Credits per request |
| --- | --- |
| Standard request | 1 |
| `render=true` (JS rendering) | +10 |
| `premium=true` (premium IPs) | +10 |
| `premium=true` + `render=true` together | 25 |
| `ultra_premium=true` + `render=true` | 75 |
| Cloudflare / Datadome / PerimeterX / Akamai bypass | +10 each |

So a rendered Zillow search page with premium IPs costs roughly 25 credits. At the Startup tier ($149 for 1M credits), that works out to about 40,000 rendered property pages per month; Hobby's 100,000 credits cover roughly 4,000. This math matters more than the headline credit number, and it's the single biggest thing most comparison articles gloss over.

One caveat before you budget: domains with custom-built scrapers can have different per-request costs, and targets occasionally change their anti-bot setup. Two safeguards handle this:

- The **Domain Cost Estimator** (Domain Multiplier) in the dashboard shows the exact credit cost of any URL before you scrape it, or you can hit the `urlcost` API endpoint directly with any parameter combination.
- Every response includes a `sa-credit-cost` header showing what that request actually consumed — worth logging so you know your true burn rate within a day of starting.
- The `max_cost` parameter caps how much a single request can spend, so one misconfigured crawl can't drain your monthly balance.

The billing rules are reasonable: you're only charged for successful requests (status 200 and 404). If you exceed your plan's concurrency limit, requests come back with a 429 and are simply turned away — there's no charge for going over.

Also worth noting: several parameters that matter for real estate work cost nothing extra — `country_code`, `wait_for_selector`, `session_number`, `output_format`, and `autoparse` (which returns auto-structured JSON at no additional credit cost).

## Which plan fits which real estate project

Here's a decision rule based on the numbers above:

- **Just testing the waters** → the free plan gives 1,000 credits per month with 5 concurrent connections, and the first 7 days after signup include 5,000 free credits for larger-scale testing. Enough to scrape a few hundred Zillow listings and validate your selectors.
- **Monitoring one metro area, a few thousand listings/month** → Hobby ($49) covers about 4,000 rendered premium pages. Tight but workable if you're efficient.
- **Regular market tracking, agent lead gen, or multi-city coverage** → Startup ($149) is the practical starting point for most working projects. 👉 Compare the Startup and Business tiers side by side
- **Serving data to an app or team, hundreds of thousands of pages** → Business ($299) or Scaling ($475). Scaling and above also unlock Pay-As-You-Go, so if you blow through your credits mid-cycle you can keep going at a fixed per-credit rate up to a spending cap you set yourself, instead of your scraper just stopping.
- **Millions of pages monthly** → Professional ($975, includes PAYG) or Advanced ($1,975). Enterprise is custom-priced for 22M+ credits and comes with a dedicated support team and Slack channel.

One trap to avoid: on **annual plans**, if you burn through your yearly credit allowance early, you can't just top up — you either wait for the next billing date, resubscribe for a fresh batch, or move to a monthly plan. If your scraping volume is spiky, monthly billing with PAYG (Scaling and up) is the safer structure. On Hobby through Professional monthly plans, there's an opt-in Auto-Upgrade that bumps you to the next tier when credits run out, usually at a better price per credit.

## Scraping a Zillow search page: the actual workflow

The official Python walkthrough gives a good picture of what a working setup looks like. The short version:

1. Sign up and grab your API key.
2. Install `requests`, `beautifulsoup4`, and `pandas`.
3. Build your target URL using Zillow's pagination pattern (a Houston search is `zillow.com/houston-tx/` for page one, `/2_p/` for page two, and so on).
4. Send the request with headers `x-sapi-api_key` and `x-sapi-render: "true"`, plus a render instruction set that waits for the price selector (`span[data-test='property-card-price']`) to appear, scrolls to the bottom, and waits 5 seconds.
5. Parse the returned HTML: prices sit in spans tagged `property-card-price`, addresses in `<address>` elements tagged `property-card-addr`, and beds/baths/square footage are the three `<li>` items inside the details list of each property card.
6. Clean the text (strip `$`, commas, whitespace) and export to CSV with pandas.

The tutorial wraps each request in a 3-attempt retry loop and includes helper functions for scrubbing inconsistent formats — realistic details you'll hit immediately in practice. Finding selectors is the usual F12 → Inspect → Elements workflow. If you'd rather skip the parsing code, `autoparse=true` handles the JSON conversion for free.

## How ScraperAPI stacks up against the alternatives

There are two broad camps in this space, and it's worth knowing which one you're shopping in.

**Ready-made real estate data APIs** — Bright Data offers a Real Estate Scraper API with 5,000 free records per month and no credit card required. These return pre-structured fields out of the box.

**Structured extraction APIs** — ZenRows has leaned into this recently: its `extract=standard` mode returns the same field names across Zillow, Redfin, and Idealista, so listings from all three land in one table without per-site mapping, and its docs show an MCP-server setup where an AI agent picks the scraping settings for you. If your entire world is major listing portals and you want zero parsing code, that's a genuinely convenient approach.

**General-purpose scraping APIs** — this is where ScraperAPI sits. You write the parsing logic (or use `autoparse`), but you're not locked into one portal's schema, and the same account covers county assessor sites, rental platforms, and anything else with a URL. For comparison: ScraperAPI's Business plan runs $299/month with 3M credits and 100 concurrent threads, with geotargeting included at no extra cost — ScrapingBee's equivalent Business plan is $249/month at the same credit and concurrency levels, but geotargeting costs extra there and only works on premium proxies (which themselves bill at 10 credits per request, per ScraperAPI's own comparison page). If international coverage matters to you, read that footnote carefully before deciding.

A practical middle ground: if your project only ever touches Zillow in a fixed schema, a dedicated endpoint is fine. If it spans multiple sources and formats, one general API with consistent handling beats stitching together several single-site products.

## Common questions before you buy

**Is there a free trial?** Yes — signing up gets you 5,000 free API credits for 7 days with no credit card required, and after that a permanent free tier of 1,000 credits/month with up to 5 concurrent connections. That's enough to test against your actual target pages before paying anything.

**Can I get a refund?** ScraperAPI offers a 7-day no-questions-asked refund — you contact support and they process it. You can also cancel anytime from the dashboard at no charge. Between the free trial and the refund window, you have roughly two weeks to evaluate it with essentially no risk. 👉 Start the free 5,000-credit trial

**What happens when I run out of credits?** Scraping pauses by default until renewal. Depending on your tier, you can enable auto-upgrade (Hobby through Professional) or Pay-As-You-Go with a spending cap (Scaling and up). Nothing gets charged automatically unless you've opted into one of those.

**Does it work outside the US?** Geotargeting is on all plans, but Hobby and Startup only support US and EU locations. For other countries, you need Business or higher.

**Are failed requests charged?** No. Only 200 and 404 responses consume credits. One caveat: if you cancel a request yourself within 70 seconds — before the API has had time to finish — it will be billed, so let slow pages run their course.

## Picking your plan, in one pass

If you're validating an idea: start free, confirm your selectors work on your target sites, and don't pay yet. If you're running an ongoing project: Startup at $149/month is the realistic floor for production Zillow or MLS scraping once you account for rendering and premium IP costs; step up to Business ($299) the moment you need non-US/EU geotargeting or more than a million credits. Annual billing saves 10%, but only lock in a year when you already know your monthly volume — otherwise the flexibility of monthly plus PAYG is worth more than the discount.

One last check before you subscribe: run your target URLs through the Domain Cost Estimator with the exact parameters you plan to use, multiply by your monthly page count, and confirm the tier you picked has headroom. Then 👉 grab a plan on ScraperAPI with the 10% annual discount — or stay monthly if you're still calibrating volume.
