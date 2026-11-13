---
name: spywinads-product-clone
description: Copy one public product page (Shopify, and some Chinese store platforms such as Shoplazza) into a Shopify-import CSV: title, prices, variants, images, plus the product's Facebook video ads with each video's real age and how many stores run it. Use whenever the user pastes a product link (…/products/…) and wants to copy, clone, import or export it to CSV: "copy this product to my Shopify", "clone this product", "export to CSV", "copy sản phẩm sang Shopify", "clone sản phẩm này", "lấy CSV sản phẩm" — even if SpyWinAds is not mentioned.
compatibility: Needs the SpyWinAds MCP connection (https://spywinads.com/mcp, free account, 30 credits a day). Runs on Claude.ai, Claude Code, ChatGPT (Plus and up), Cursor, Codex.
license: MIT
---

# Shopify product clone — copy a product page to a Shopify CSV

Data comes from the **SpyWinAds MCP**: a library of Meta video ads shown in the US, scanned by SpyWinAds users. Each video is fingerprinted, so the same video running in many stores is recognised as one.

## 0. Check the connection first

No tool named `spywin_*` available → the user has not connected SpyWinAds yet. Do not guess numbers or analyse from memory: the user
will trust those numbers. Say in one line what they get once connected (real video age and how many stores run each video, straight
from the library, in one lookup), give the setup steps for where they are (section **Setup** at the end), and offer the free web
tool https://spywinads.com/clone-shopify-free meanwhile. Wait until they are connected.

`found: false` → call `spywin_queue_page_scan` only if it is in your tool list (ask the user one short question first), then follow
with `spywin_get_scan_status(request_id=…)` and look up again. Not in your tool list → give the free scan link the tool returned.

Each SpyWinAds tool call uses 1 credit (free: 30 a day, paid plans: 100), so call the right tool once per input. If a tool says out of credits or
not signed in, repeat that message and link `https://spywinads.com/developers`.

Answer in the user's language, including table headers.

## 1. Read the product

Input: a product URL (`https://store.com/products/handle`). A store home page or collection is not a product: ask for one product link.

Call `spywin_export_shopify_product(url)`. You get title, prices, variants, images, `csv_url` (valid 24 hours) and
`video_ads_in_library`. It reads stores that block ordinary web readers. "Could not read that product" → not a public product page:
ask for another link, do not retry the same one.

## 2. Report

1. **CSV link first**, with: "Shopify admin → Products → Import → choose the file. The link works for 24 hours."
2. Short card: title, price range (`price_min`–`price_max`), number of variants and options, images. Mention an urgency line in
   the title ("Last Day – 60% OFF") only if there is one.
3. **Video ads of this product** (if any):

| Video age | Stores running now / ever | Live ads |
|---|---|---|

4. Remind once: rewrite the title and description and use your own photos where you can; copied text and images can bring
   copyright complaints. Videos belong to their original owners.

**Next step** — offer in plain words, and if the user picks one, call the tool yourself: the other stores running the top video →
`spywin_lookup_video_ad(video_id=…)` · the source store → `spywin_spy_store_ads(domain)`.

## Read the numbers correctly

- **Real video age** = `video_first_on_meta`, the day the video file was first uploaded to Meta. Show it as date + days, counted
  from today: "22 Sep (52 days)". The ad start date resets each time a store pauses and restarts an ad; the file date does not, so
  say "video age", not "ad running for". `video_first_on_meta` empty → the file date is unknown: give `first_ad` and say it is the
  first ad date, not the video age.
- **Scope**: `live_ads` counts only the store / Page / product you looked up (whole library for a single-video lookup).
  `stores_running_now`, `stores_ever` and `pages_running_now` count **every store in the library**. So a paused store (0 live ads)
  can show a video that 4 other stores still run: say exactly that ("paused here, still running in 4 other stores"), it is not a
  contradiction.
- A product name that looks like a code (`ymtldysdym1`) is the store's URL handle: say the name is not readable and give the
  landing page. Same product with several videos → one row, "2 videos".
- Group brands by the **landing-page domain**, never by ad copy. Two stores with the same hook are still two brands.
- The library only sees a **minimum count of ads observed**: no spend, reach, orders or revenue. Many ads / many stores / a long run
  means *being invested in or copied*, not *profitable*. Say "worth a look because…", never "winning", "profitable", "making sales".
- Data = Meta ads shown in the US, scanned by SpyWinAds users. "Earliest store" = earliest in the library, not proven owner.

## Limits

This skill only reads SpyWinAds data: it does not scrape Facebook and never logs in to the user's Facebook account.

## Setup (give this to users who are not connected)

Sign-up happens at the login step: free, 30 credits a day, no card.

- **Claude.ai** (every plan incl. Free, 1 custom connector): Customize → Connectors → + → Add custom connector → URL
  `https://spywinads.com/mcp` → Connect → sign in → Allow.
- **ChatGPT** (Plus / Pro / Business; Free cannot add custom MCP yet, suggest Claude.ai Free): Settings → Apps & Connectors → Advanced →
  Developer mode → Create → URL `https://spywinads.com/mcp`, OAuth → sign in → Allow.
- **Claude Code**: `claude mcp add --transport http spywinads https://spywinads.com/mcp`, then type `/mcp`, pick spywinads and
  finish the sign-in in the browser (adding it alone does not connect it).
- **Cursor / Codex / scripts**: API key at `https://spywinads.com/developers`, header `Authorization: Bearer <key>` (never in the URL).

Want the full analysis (several stores, ad angles, Shopify CSV in one go)? Skill **spywinads-soi-ads**:
https://github.com/SpyWinAds/soi-ads-skill
