# ecommerce web scraping: How to Pull Product Data Without Getting Blocked, What Credits Really Cost, and Which ScraperAPI Plan Fits Your Job

Ecommerce web scraping is how price-monitoring tools, market analysts, and competitive intel teams collect product data — prices, stock, ratings, reviews — from Amazon, Walmart, and thousands of retail sites, then turn it into data they can actually act on. The hard part has never been parsing HTML. It's getting a clean response at all: rotating IPs, solving CAPTCHAs, rendering JavaScript, and doing it thousands of times a day without your scraper collapsing.

One way to handle all of that is ScraperAPI: you send a GET request with your target URL and an API key, and the service handles proxy rotation, retries, CAPTCHA bypass, and optional JS rendering on its end. It also ships structured endpoints that return parsed JSON for Amazon product and search pages, Walmart, and Google Shopping — which matters a lot for ecommerce work, where sites get redesigned and your own parser breaks every few months.

Here's the short version if you're deciding quickly:

> **Quick pick:** Most people doing ecommerce scraping land on the **Hobby plan ($49/month, 100,000 credits, 20 concurrent threads)** for small projects, or **Business ($299/month, 3M credits, global geotargeting)** if you need data from outside the US and EU. Every paid plan starts with a 7-day free trial of 5,000 credits, no credit card required. 👉 [See ScraperAPI's full plan list and start the free trial](https://www.scraperapi.com/pricing?fp_ref=coupons)

## What ecommerce scraping actually requires

Product pages are the worst-case scenario for a naive scraper. Three things break simple HTTP requests:

1. **JavaScript rendering.** Many retail sites load prices and stock into the DOM after the initial HTML arrives. A plain `requests.get()` gets you a page full of empty divs.
2. **Bot protection.** Amazon, Walmart, and larger retailers sit behind systems that fingerprint your requests and serve CAPTCHAs or blank pages.
3. **IP rotation and rate limits.** Pull the same site from one IP a few thousand times and you'll be blocked long before your job finishes.

ScraperAPI wraps all three. A standard request costs 1 credit. Turning on `render=true` for JavaScript rendering costs about 10 credits. Routing through the premium or ultra-premium residential/mobile pools — the tiers you need against harder anti-bot systems — costs 10 to 25 credits per request. Sites protected by Cloudflare, Datadome, or PerimeterX add another 10 credits per request when the service successfully bypasses them. All of this is documented in the official pricing page's credit schedule, and you can check the exact cost of any URL with the Domain Cost Estimator in the dashboard before you burn through your quota.

There's also a `max_cost` parameter you can set per scrape, which caps how many credits a single request can consume. If you're running an unattended job overnight, that's the difference between a normal bill and a surprise.

## The full plan list, current pricing

ScraperAPI currently sells seven paid tiers, all with the same core feature set: JS rendering, premium proxies, JSON auto-parsing, rotating proxy pools, custom headers, CAPTCHA and anti-bot detection, custom sessions, desktop and mobile user agents, automatic retries, unlimited bandwidth, and a 99.9% uptime guarantee. Annual billing takes 10% off every plan.

| Plan | Monthly | Annual (per month) | Credits | Threads | Link |
| --- | --- | --- | --- | --- | --- |
| Hobby | $49/mo | $44.10/mo | 100,000 | 20 | [Start with Hobby](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Startup | $149/mo | $134.10/mo | 1,000,000 | 50 | [Check Startup pricing](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Business | $299/mo | $269.10/mo | 3,000,000 | 100 | [Get the Business plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Scaling | $475/mo | $427.50/mo | 5,000,000 | 200 | [Compare Scaling vs Business](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Professional | $975/mo | $877.50/mo | 10,500,000 | 300 | [View Professional plan](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Advanced | $1,975/mo | $1,777.50/mo | 21,500,000 | 500 | [See Advanced plan details](https://www.scraperapi.com/pricing?fp_ref=coupons) |
| Enterprise | Custom | Custom | 22M+ | 500+ | [Talk to sales](https://www.scraperapi.com/pricing?fp_ref=coupons) |

The second-tier differences that don't fit in the table:

- **Geotargeting:** Hobby and Startup are limited to US and EU exit points only. Country-level targeting across 50+ countries starts at Business.
- **Analytics history:** Hobby and Startup keep 30 days of usage history; Business and above keep it indefinitely.
- **Pay-as-you-go overage:** available on Scaling and up. When you blow through your monthly credits, you keep scraping at a fixed per-credit rate up to a spending cap you set. On Hobby through Professional, the alternative is auto-upgrade, which bumps your account to the next plan when credits run out.
- **Priority support** starts at Professional; Advanced adds priority routing so your requests jump the queue.

If your credit balance runs out with none of those options enabled, scraping simply pauses until the next renewal. Annual plans can renew early for a fresh batch of credits.

## The real cost math most reviews skip

This is the part that actually determines whether a plan works for you, and it's where the headline credit counts mislead people.

Credits and requests are different units. A "1,000,000 credits" plan doesn't scrape a million pages unless every page costs 1 credit. On a real ecommerce job, they usually don't.

Take Amazon product pages. The structured Amazon endpoint costs **5 credits per call**. So on Hobby's 100,000 credits, you're pulling about **20,000 product pages per month** — which works out to roughly **$2.45 per 1,000 product records** on the $49 plan. That's a genuinely useful number for budgeting, and you can derive it yourself for any tier: credits ÷ 5 = Amazon product pages.

Now compare a raw-HTML job on a JS-heavy retail site. `render=true` costs about 10 credits, and if the site needs the premium pool too, you're at 10–25 credits per page. A 2026 independent review ran this math on the Startup plan and found that a job needing rendering plus premium routing could cost 20–30x the headline per-credit rate — meaning 1,000,000 credits might cover only 40,000–100,000 actual pages on heavily protected targets. Their one-line advice is worth keeping: budget against your real per-page cost, not the credit count on the pricing page.

The practical takeaway: before picking a plan, list your target sites, note which ones need rendering and premium routing, and multiply. The Domain Cost Estimator in the dashboard gives you the exact number per URL, so there's no guesswork.

Two mitigating factors matter here. Failed requests don't cost credits — you only pay for successful responses, so a burst of blocks during a test run won't drain your balance. And the 7-day trial gives you 5,000 real credits to measure your actual per-page cost against your own targets before you commit to anything. 👉 [Test your real per-page cost with 5,000 free credits](https://www.scraperapi.com/?fp_ref=coupons)

## The Amazon endpoint: why it's the main reason ecommerce teams pick this

Writing and maintaining your own Amazon parser is a treadmill. Amazon changes markup, you fix selectors, it changes again. The structured Amazon Product API endpoint outsources that problem: you pass an ASIN, a TLD, and optionally a country code, and you get back JSON with the product name, price, variant details (size, color, and other options), feature bullets, image arrays, category path, seller and shipping info, coupon presence, best-seller rank, and all publicly available reviews for that ASIN.

The endpoint covers **22 Amazon marketplaces** — amazon.com, co.uk, de, fr, co.jp, in, com.au, com.mx, com.br, ae, and the rest. One detail from the official docs that trips people up: ASINs are marketplace-specific, and if you need to scrape one Amazon domain from another country (say, amazon.com from Canada to get Canadian shipping info), you must specify **both** the TLD and the country code. Output comes as JSON by default, with CSV available.

For a price-monitoring pipeline, this changes the architecture. Instead of proxy pools plus a headless browser plus a parser you babysit, it's one GET request per ASIN at a known, flat 5-credit cost. For sellers tracking their own listings across marketplaces, the `is_coupon_exists` and `ships_from`/`sold_by` fields alone cover things you'd otherwise scrape piecemeal.

If your targets are Amazon, Walmart, or Google Shopping, start here rather than with the generic endpoint. 👉 [Try the Amazon structured endpoint on the free trial](https://www.scraperapi.com/?fp_ref=coupons)

## Where ScraperAPI fits — and where it doesn't

Honest limitations, all documented or independently verified:

- **Sticky sessions cap out around one hour.** The `session_number` parameter keeps the same IP across a request sequence, which is enough for paginated catalog crawls. If you need an identity to persist across days — account-based scraping, ad verification — this won't do it.
- **No manual IP selection.** You can't pin specific IPs or ASNs; you trust the rotation logic.
- **Hobby's 20 concurrent threads** fill up fast on production jobs. Passing the limit returns a 429 (no charge, but the request is rejected rather than queued), so a heavy batch job slows to a crawl.
- **Heavily protected platforms at volume** are the weak spot per the 2026 review's verdict: great for standard ecommerce and public-data targets, worse for high-volume scraping against aggressive anti-bot systems.
- **Support on the low tiers** is email/ticket only — no live chat or phone until the higher plans.

How it stacks up against alternatives from an October 2026 same-day comparison test of five Amazon scraper APIs (fresh accounts, same ASIN, same day): ScraperAPI returned the full Amazon page as raw HTML (1.8 MB, HTTP 200, no CAPTCHA, 4.2s latency) on its 7-day free trial; one competitor — Bright Data — never granted its advertised free credits and timed out, and another returned HTTP 502 twice. Only two of the five platforms returned clean structured JSON out of the box. The point for your decision: free tiers advertised on marketing pages don't always match what you actually receive, and ScraperAPI's trial delivered exactly what it promises.

## FAQ

**How many Amazon product pages can I scrape per month on each plan?**
Divide the credit allowance by 5. Hobby: ~20,000. Startup: ~200,000. Business: ~600,000. Scaling: ~1,000,000. Those assume the structured endpoint only — add rendering or premium routing on other targets and your per-page cost rises accordingly.

**Do failed requests cost credits?**
No. You only pay for successful responses, and hitting your concurrency limit (429) is free too.

**Is there a truly free option?**
Yes. Beyond the 7-day trial, there's a permanent free plan with 1,000 credits and up to 5 concurrent connections — enough to validate your approach, not enough for production.

**What happens when I run out of credits mid-month?**
Scraping pauses by default. Auto-upgrade (Hobby through Professional) moves you to the next plan automatically, and pay-as-you-go (Scaling and up) continues at a fixed per-credit rate under a cap you set.

**Do I need to enter a credit card for the trial?**
No. The 7-day trial with 5,000 credits requires no card.

## Which plan to actually pick

Match your situation to a tier:

- **Testing an idea or scraping under ~20k product pages a month:** free trial first, then Hobby at $49. If all your targets are US/EU and you don't need JS rendering on most pages, 100,000 credits goes further than it looks.
- **Small team, regular price monitoring, still US/EU only:** Startup at $149. Ten times the credits for three times the price.
- **Production workload that needs data from Asia, LATAM, or anywhere outside US/EU:** Business at $299 is the minimum tier with country-level geotargeting. Don't buy Hobby or Startup and expect global coverage — it's not there.
- **Spiky volume that swings month to month:** Scaling at $475, mainly because pay-as-you-go overage means a heavy month doesn't shut your pipeline down mid-run.

One last thing before you check out: run your three most important target URLs through the Domain Cost Estimator during the trial, write down the actual credits-per-page for each, and size your plan from that number. It takes ten minutes and it's the only reliable way to know whether $49 or $299 is the right tier for your job. 👉 [Pick your plan and start with 5,000 free credits](https://www.scraperapi.com/pricing?fp_ref=coupons)
