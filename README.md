# Crawl Entire Website: Why the First Run Misses Pages, What 5,000 Pages Really Costs, and Which ScraperAPI Plan Covers It (Full Pricing Table)

A crawl that returns 800 pages from a 4,000-page site usually isn't a crawling failure. It's a configuration failure, and it comes from three settings that ship with quiet defaults: the URL rule that decides what counts as part of the site, whether you cap depth or spend, and the per-page credit multiplier your scraping options switch on. ScraperAPI charges 1 credit for a plain page and 75 for an Ultra Premium page that also needs JavaScript rendering. Firecrawl charges 1 credit per page and stops a job at 10,000 pages unless you raise the limit.

If you want to crawl an entire website without babysitting the job or blowing a budget, those three numbers are the whole decision. What follows covers what ScraperAPI's crawler actually follows, what a page really costs after your options are applied, and where the plan boundaries sit. All prices are in USD and match ScraperAPI's pricing page as of this writing.

> **Most teams should start on Business:** $299/month, or $269.10/month billed annually, for 3,000,000 credits and 100 concurrent threads. That covers about 300,000 JavaScript-rendered pages, or 3,000,000 plain HTML pages, and it's the cheapest plan with country-level geotargeting. A single site under roughly 10,000 rendered pages a month fits inside Hobby at $49.
>
> 👉 [See current ScraperAPI pricing and credit allowances](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Every ScraperAPI plan on the pricing page

ScraperAPI sells credits, not pages. The annual column is the effective monthly rate once the 10% annual discount is applied.

| Plan | Credits/month | Concurrent threads | Monthly price | Annual (per month) | Link |
| --- | --- | --- | --- | --- | --- |
| Hobby | 100,000 | 20 | $49 | $44.10 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | 1,000,000 | 50 | $149 | $134.10 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | 3,000,000 | 100 | $299 | $269.10 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling (Most Popular) | 5,000,000 | 200 | $475 | $427.50 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | 10,500,000 | 300 | $975 | $877.50 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | 21,500,000 | 500 | $1,975 | $1,777.50 | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | 22,000,000+ | 500+ | Custom | Custom | [View plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |

The four columns below are what separate neighboring plans once credits stop being the constraint.

| Plan | Geotargeting | Analytics history | Pay as you go | Support |
| --- | --- | --- | --- | --- |
| Hobby | US and EU only | 30 days | No | Standard |
| Startup | US and EU only | 30 days | No | Standard |
| Business | Country-level | Unlimited | No | Standard |
| Scaling | Country-level | Unlimited | Yes | Standard |
| Professional | Country-level | Unlimited | Yes | Priority |
| Advanced | Country-level | Unlimited | Yes | Priority routing |
| Enterprise | Country-level | Unlimited | Yes | Dedicated team, Slack |

Three billing rules affect a crawl more than the headline numbers. Credits reset at each renewal and nothing rolls over, so a plan sized for your biggest month wastes money in every smaller one. Pay-as-you-go exists on Scaling and above only; on Hobby, Startup and Business an exhausted balance returns HTTP 429 instead of quietly billing overage. And the annual discount is exactly 10%, which means the monthly and annual prices never drift apart in ways you have to re-check.

For crawling specifically, credits and thread count are the only two columns that decide whether a job finishes on time. 20 threads against a 500,000-page rendered target is what people upgrade over, not the storage or the support tier.

## How ScraperAPI's crawler decides what belongs to the site

You hand the crawler a seed URL and a required `urlRegexpInclude` pattern. It follows only links matching that pattern, and `urlRegexpExclude` cuts sections you don't want. ScraperAPI's own crawler reference is blunt about the failure mode: a broad pattern like `.*` will follow external links and crawl the internet. Anchor the include pattern to your host and the crawl stays on-domain by construction rather than by luck.

The other scope control is the one most people pick wrong. You must supply either `maxDepth` (the seed URL is depth 0) or `crawlBudget` (the maximum credits the job may consume).

- Use `maxDepth` when the site's shape is known and bounded, or when you want a specific section, say three hops from a landing page.
- Use `crawlBudget` when the site is large and link density is unpredictable, because it caps spend regardless of how many links exist.
- Set both for exploratory runs. `maxDepth: 3` with `crawlBudget: 500` stops at whichever limit hits first.

ScraperAPI's documentation recommends always setting `crawlBudget` even when you're using depth. That single line is the difference between a crawl that ends and one that keeps finding pages at your expense.

Per-page behavior travels through `apiParams`: `country_code`, `render`, `premium`, `ultra_premium`, `device_type`, `output_format`. Anything you enable there multiplies the credit cost of every page the crawler touches, so it belongs in the budget conversation before the job starts, not in the invoice afterwards.

Two cases where the crawler is the wrong tool. If you already have a URL list, ScraperAPI's Async API is cheaper and more predictable than link discovery. If you need one page, use the Standard API. The crawler also only reaches publicly available pages, so anything behind a login is out.

## What a page costs in credits, and why 5,000 pages isn't 5,000 credits

| Request type | Credits | Notes |
| --- | --- | --- |
| Standard page | 1 | Per request |
| Amazon, Walmart, eBay | 5 | Per request |
| Google or Bing (incl. subdomains) | 25 | Per request |
| LinkedIn | 30 | Per request |
| Bot-protection bypass | +10 | Added when ScraperAPI activates bypass logic |
| JS rendering (`render=true`) | 10 | Per request |
| Premium proxies (`premium=true`) | 10 | Per request |
| Screenshot | 10 | Per request |
| Premium proxies + JS rendering | 25 | Per request |
| Ultra Premium (`ultra_premium=true`) | 30 | Paid plans only |
| Ultra Premium + JS rendering | 75 | Paid plans only |
| Geotargeting, sessions, device type, output format, auto parsing | 0 | No extra cost |

Run the same 5,000-page site through four configurations and the plan you need changes every time.

Plain HTML with no extras costs 5,000 credits. Hobby's 100,000 covers that job twenty times in one month, which is why "how many pages" is the wrong question on its own.

Turn on JavaScript rendering and the same crawl costs 50,000 credits. Still Hobby, with half the month's balance left.

Add premium proxies to rendering and it's 25 credits per page, or 125,000 credits. That crosses Hobby's entire monthly allowance in a single job. Startup at 1,000,000 credits absorbs eight of those crawls.

Go all the way to Ultra Premium with rendering at 75 credits per page and 5,000 pages becomes 375,000 credits. Business at 3,000,000 handles roughly 40,000 pages a month at that setting.

The domain matters as much as the options. Crawling 5,000 Google result pages at 25 credits each is 125,000 credits, which puts a SERP project on Startup before a single page of JavaScript rendering enters the picture. The dashboard's Domain Cost Estimator is the authoritative number for any specific URL, and it's worth checking before you commit to a plan size.

For a rough cross-check, Firecrawl bills the same 5,000-page crawl at 5,000 credits in markdown and 25,000 in JSON, since its JSON format adds 4 credits per page.

👉 [Check which plan covers your monthly credit total](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Firecrawl and Cloudflare run the same job with different defaults

Two other APIs accept a starting URL and return the whole site. They disagree on what "the whole site" means, and on how failures reach you.

|  | ScraperAPI Crawler | Firecrawl `/crawl` | Cloudflare Browser Rendering `/crawl` |
| --- | --- | --- | --- |
| Scope control | Required URL regex include, optional exclude, `maxDepth` or `crawlBudget` | Path regex, `crawlEntireDomain`, sitemap modes, `limit` default 10,000 | Depth, page limits, wildcard include/exclude |
| Per-page cost | 1 credit standard, up to 75 with options | 1 credit/page, +4 for JSON, 1 for PDF pages | Not published for this endpoint |
| JavaScript | `render=true`, 10 credits/page | On by default | Headless by default, `render: false` for static HTML |
| Delivery | Status polling, `callbackUrl` webhook, schedules | Polling, WebSocket, webhooks, Monitor | Async job, poll by job ID |
| Availability | Paid plans; free accounts capped at depth 1 | Free tier: 500 searches or 1,000 pages | Workers Free and Paid, open beta |

Firecrawl's most expensive default is geographic rather than financial. `/crawl` follows child paths of the URL you pass and ignores sibling or parent paths. Start at `example.com/docs/` and `example.com/blog/` stays out of scope until you set `crawlEntireDomain: true`. Subdomains need `allowSubdomains`, off-domain links need `allowExternalLinks`, and even then external links are followed one hop with homepage links skipped.

Its result layer has three traps worth knowing before you trust a page count. A page the target site serves as 404 still appears in the `data` array, with the real HTTP status in `metadata.statusCode`. The `/crawl/{id}/errors` endpoint is documented as not guaranteed to be a complete list of failures. And the `next` pagination cursor appears whenever status isn't `completed`, so failed or cancelled jobs keep offering more results. Loop on `status`, not on `next`. If you need the same URL set on every run, `sitemap: "only"` restricts discovery to the sitemap plus the start URL, because sitemap discovery is queued outside the concurrency limit and `data` is ordered by completion time rather than discovery order.

Cloudflare's endpoint, in open beta and available on both Workers Free and Paid, is a verified bot that honors `robots.txt` including `crawl-delay` and AI Crawl Control. It cannot bypass Cloudflare's own bot detection or captchas and identifies itself as a bot, so sites behind aggressive protection are simply out of reach. Two features are genuinely useful for repeat work: `modifiedSince` and `maxAge` skip pages that haven't changed or were fetched recently, and `render: false` fetches static HTML without launching a browser. Cloudflare has not published a per-page price for the endpoint, so any claim about which service is cheaper per page is unsupported.

## Scheduling, webhooks, and repeat runs

ScraperAPI's crawler accepts a `schedule` of `once`, `hourly`, `daily`, `weekly` or `monthly`, and a `callbackUrl` for results. Scheduling requires a paid plan; free accounts are limited to one-time crawls at `max_depth: 1`. The callback guidance is worth repeating because it involves data leaving your account: a crawl can produce hundreds or thousands of payloads containing personal or proprietary content, all transmitted to the URL you name. Use HTTPS and get explicit approval before pointing a job at an endpoint you don't control.

For long runs, a webhook beats polling `crawler_job_status` in a loop. Keep the returned job ID so the delete call is one request away when a crawl starts consuming more credits than you planned.

## Errors that stop a crawl halfway

| Code | Meaning | What to do |
| --- | --- | --- |
| 400 | Malformed request | Check required fields and regex syntax |
| 401 | Invalid API key | Check the key |
| 403 | Credits exhausted, or free-plan depth limit | Top up or reduce `maxDepth` / `crawlBudget` |
| 429 | Too many concurrent jobs | Wait and retry |
| 500 | Transient failure | Retry with backoff |

403 is the one to read carefully, since it covers both an empty balance and the free-plan depth cap. Check the balance before rewriting a regex that was fine. And note that Hobby, Startup and Business return 429 rather than an overage charge when credits run out, which is friendly until a production crawl stops mid-job on the 28th of the month.

👉 [Start with Hobby if your crawl fits in 100,000 credits](https://www.scraperapi.com/pricing?fp_ref=coupons)

## Which plan to buy

Match the plan to the multiplier, not to the page count you have in mind.

- **Under 10,000 JavaScript-rendered pages a month, or under 100,000 plain pages:** Hobby at $49. It's the plan most single-site crawls actually need, and upgrading before you've measured your per-page cost is the most common waste.
- **Several sites, Amazon or SERP targets, or more than 50 concurrent threads:** Business at $299. It's also the first plan with country-level geotargeting and unlimited analytics history, both of which matter when you're crawling localized versions of the same site.
- **Crawls that would otherwise stall mid-month:** Scaling at $475 is the first plan with pay-as-you-go, so a spike doesn't turn into a 429.
- **10 million credits and up:** Professional at $975 or Advanced at $1,975, with priority support attached.

Two things to check before you pay. Run a 50-page test with the exact `apiParams` you intend to use, then multiply the credits consumed by your real page count; that number, not the marketing page count, decides the tier. And confirm whether you need recurring schedules, since those are paid-plan only and a one-time crawl on a small plan won't reveal the gap until month two.

Credits don't roll over, so buy for the month in front of you and move up when a job actually needs it.

👉 [Compare all seven ScraperAPI plans](https://www.scraperapi.com/pricing?fp_ref=coupons)
