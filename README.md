# Threads Scraper — Threads.net Profile & Posts Extractor (Pay Per Event)

[![Apify Actor](https://img.shields.io/badge/Apify-Actor-blue?logo=apify)](https://apify.com/red.cars/threads-scraper-ppe)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

Extract Threads profiles, posts, AND comment blocks in one run — then pay only for the data you actually get. Unlike flat-fee Threads scrapers that charge per result whether or not you get useful data, this actor charges **per event**: $0.08 per profile, $0.015 per post, $0.025 per comment block. Empty results cost you nothing beyond the $0.00005 actor start.

**Apify Store:** https://apify.com/red.cars/threads-scraper-ppe

---

## What This Actor Does

This Threads scraper extracts public data from Meta's Threads.net platform: full profile details (bio, follower count, verification, profile picture) plus each profile's recent posts with complete engagement metrics — likes, replies, reposts, quote posts, media URLs, hashtags, and mentions. Optionally extract comment blocks per post.

One actor covers what profile-only scrapers split across tools: **profile + posts + comments in a single run.**

Search intent keywords: "threads scraper", "threads.net scraper", "threads profile posts extractor", "threads data extractor", "threads posts scraper", "threads profile scraper", "scrape threads.net", "threads engagement metrics API".

---

## Why Pay Per Event (PPE)?

| | Flat-fee scrapers | This actor (PPE) |
|---|---|---|
| You pay for | Every item, useful or not | Only data actually extracted |
| Empty result | Charged | **$0** |
| Post count control | Fixed by the tool | You set `maxPosts` (1–1000) |
| Health check | Charged as a run | **Free** (`checkOnly: true`) |
| Typical run (1 profile + 20 posts) | ~$0.10 flat, posts-only | **$0.38** — profile + 20 posts with comments and full engagement data |

A typical run: 1 profile ($0.08) + 20 posts ($0.30) = **$0.38**, about $0.018 per result — premium-priced because each result carries profile, post, AND comment data with structured engagement fields.

## Pricing

| Event | Price | Charged for |
|---|---|---|
| Actor start | $0.00005 | One-time per run |
| `scrape_profile` | $0.08 | Each Threads profile scraped |
| `scrape_posts` | $0.015 | Each post extracted |
| `scrape_comments` | $0.025 | Each comment block extracted |

The `checkOnly` health-check handshake always exits free — before any charge event fires.

---

## Comparison

If you compare tools that extract Threads posts and replies on the Apify Store, today's top listings:

| Actor | Users | Pricing | Coverage |
| --- | --- | --- | --- |
| Threads Replies Scraper (futurizerush) | 2,085 | $2.50 per 1,000 results | Replies |
| Threads Scraper (sourabhbgp) | 418 | $2.00 per 1,000 results | Profiles, posts, search, replies |
| Threads Post Scraper (mikolabs) | 1 | $1.80 per 1,000 results | Posts and replies |

This actor bills per event instead of per result: $0.08 per profile, $0.015 per post, $0.025 per comment block, and $0.00005 per run start. Per-event billing charges you for the data you get. A per-result plan charges for every row, useful or not. One run here returns profile details, posts, and comment blocks together, and the verified-only and minimum-engagement filters apply before billing.

## Who It's For

- **Social media analysts** monitoring brand presence and engagement on Threads
- **Influencer researchers** vetting creators by follower count, verification, and engagement rate
- **Content strategists** tracking post performance, hashtags, and top-performing formats
- **Lead generation teams** building prospect lists from public Threads profiles
- **AI agents** via Apify MCP integration for automated social-data pipelines

---

## Quick Start

```bash
# Option 1: Apify Console (no code)
# Open https://apify.com/red.cars/threads-scraper-ppe and click Run

# Option 2: API (cURL)
curl -X POST "https://api.apify.com/v2/acts/red.cars~threads-scraper-ppe/runs?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["nike", "cnn"], "maxPosts": 20, "includeComments": false}'

# Option 3: Node.js
const { ApifyClient } = require('apify-client');
const client = new ApifyClient({ token: 'YOUR_TOKEN' });
const run = await client.actor('red.cars/threads-scraper-ppe').call({
  urls: ['nike', 'cnn'],
  maxPosts: 20,
  includeComments: true,
  maxComments: 20,
});
```

### Free Health Check (before any paid run)

```bash
curl -X POST "https://api.apify.com/v2/acts/red.cars~threads-scraper-ppe/runs?token=YOUR_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{"urls": ["nike"], "checkOnly": true}'
```

Completes in under 5 seconds with zero charge events — verify availability before committing to a paid run.

---

## Input Parameters

| Parameter | Type | Default | Description |
|---|---|---|---|
| `urls` | string[] | `["nike", "cnn"]` | Threads usernames or profile URLs (`nike`, `@cnn`, `https://www.threads.net/@zuck`) |
| `maxPosts` | integer | 20 | Posts per profile (1–1000) |
| `includeComments` | boolean | false | Extract comment blocks per post |
| `maxComments` | integer | 20 | Comments per post (when comments enabled) |
| `onlyVerified` | boolean | false | Extract only verified (blue checkmark) accounts |
| `minEngagement` | integer | 0 | Skip posts with total engagement below this |
| `proxyType` | string | RESIDENTIAL | `DATACENTER` faster; `RESIDENTIAL` most reliable |
| `debugMode` | boolean | false | Minimal extraction for testing, guaranteed < 300s |
| `exportFormat` | string | json | `json`, `csv`, or `markdown` (token-efficient, LLM-ready) |
| `checkOnly` | boolean | false | Free health-check handshake (no charges) |

---

## Example Output

**Profile item:**
```json
{
  "type": "profile",
  "username": "nike",
  "displayName": "Nike",
  "bio": "Just Do It",
  "followers": 9210000,
  "following": 52,
  "postsCount": 1483,
  "isVerified": true,
  "profilePictureUrl": "https://...",
  "scrapedAt": "2026-09-27T02:15:40.839Z"
}
```

**Post item:**
```json
{
  "type": "post",
  "username": "nike",
  "postId": "3559898176278369487",
  "postText": "Save the date: Tuesday 11th February, 2pm-8pm!",
  "postUrl": "https://www.threads.net/@nike/post/DFnTIe5ohTP",
  "timestamp": "2026-02-10T14:00:00Z",
  "likes": 12045,
  "replies": 312,
  "reposts": 890,
  "quotes": 45,
  "mediaUrls": ["https://..."],
  "hashtags": ["PureGym"],
  "isReply": false,
  "totalEngagement": 13292,
  "engagementSummary": { "totalEngagement": 13292, "hasMedia": true, "commentBlockIncluded": true },
  "scrapedAt": "2026-09-27T02:15:40.839Z"
}
```

---

## Dataset Schema Preview

Every dataset item is typed and exportable as JSON, CSV, Excel, or HTML:

| Field | Type | Description |
|---|---|---|
| `type` | string | `profile` or `post` |
| `username` | string | Threads username (no `@`) |
| `displayName` | string | Full display name |
| `bio` | string | User biography |
| `followers` / `following` / `postsCount` | integer | Profile metrics |
| `isVerified` | boolean | Blue checkmark status |
| `profilePictureUrl` | uri | Profile picture |
| `postId` | string | Unique post identifier |
| `postText` | string | Post content |
| `postUrl` | uri | Direct link to the post |
| `timestamp` | date-time | Post creation time |
| `likes` / `replies` / `reposts` / `quotes` | integer | Engagement metrics |
| `mediaUrls` | string[] | Image/video URLs |
| `hashtags` / `mentions` | string[] | Tags and mentions |
| `totalEngagement` | number | Summed engagement on the post |
| `engagementSummary` | object | {totalEngagement, hasMedia, commentBlockIncluded} |
| `scrapedAt` | date-time | Extraction timestamp |

---

## Use Cases

- **Social listening** — track brand mentions and sentiment across Threads in real time
- **Competitor monitoring** — watch rivals' posting cadence and engagement weekly
- **Influencer vetting** — compare follower counts, verification, and engagement before partnerships
- **Content research** — identify top-performing formats by likes/replies/reposts
- **Trend detection** — follow hashtags and high-engagement posts by topic

---

## FAQ

**How is this different from other Threads scrapers?**
Combined profile + posts + comment data in one run, with pay-per-event billing: you're charged only for records actually extracted, and empty results cost nothing.

**What does a typical run cost?**
1 profile + 20 posts ≈ $0.38. Add 20 comment blocks for another $0.50 if you enable comments. The free `checkOnly` health check costs nothing.

**Do I need cookies or a Threads login?**
No. The actor extracts only publicly visible data — no session cookies required.

**Can I scrape private accounts?**
No. Only public profiles and posts are supported.

**Is there a free way to test?**
Yes — set `checkOnly: true`. The run exits in under 5 seconds with zero charge events.

**What formats can I export?**
JSON, CSV, Excel, HTML, and XML via the Apify dataset Storage tab or API.

**Can AI agents use this?**
Yes — connect via the Apify MCP server (`https://apify.actor/mcp`) and call the actor as an MCP tool.

---

## Legal

This actor is provided for legitimate data collection purposes only. It extracts only publicly shared data — no private user data (emails, gender, location). Users must comply with Meta's Terms of Service and applicable privacy laws; results may contain personal data, so scrape only with a legitimate reason.

---
*red.cars Intelligence Fleet • [apify.com/red.cars](https://apify.com/red.cars)*

*Keywords: threads scraper, threads.net scraper, threads profile posts extractor, threads data extractor, threads posts scraper, threads profile scraper, scrape threads.net, meta threads scraper, threads engagement metrics, threads comments scraper, social media monitoring threads, influencer research threads, threads api alternative, extract threads data, threads lead generation.*

> [ScraperFleet data page](https://scraperfleet.com/actors/threads-scraper-ppe/)

Browse the other 113 ScraperFleet data pages at https://scraperfleet.com/
