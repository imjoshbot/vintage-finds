# Vintage Finds

Data-only repo (public, no sensitive info — just secondhand-listing search results)
powering the "Vintage Hunt" section of the [Horowitz holiday gift guide](https://github.com/imjoshbot/2027-gift-guide).

`data/vintage-finds.json` is fetched at runtime by the gift guide's `/vintage` page
(via `raw.githubusercontent.com`, no build step needed), and is refreshed daily by
a scheduled cloud agent ("Vintage Hunt" routine). It currently runs a full
English-Ralph-Lauren / Ivy / country wardrobe build-out across 8 categories: sport
coats & blazers, suits, knitwear, shirts, trousers, shoes, ties, and outerwear.

## Schema

```jsonc
{
  "updatedAt": "ISO timestamp of the last run",
  "styleGuide": "Shared aesthetic mission, sizing, shopping approach and reporting bar that applies across every search below — read this first, then each search's own criteria.",
  "searches": [
    {
      "slug": "kebab-case-id",
      "title": "Human title shown as a section heading",
      "benchmark": "One-line description of the ideal/reference item for this category",
      "criteria": "The category-specific brief — brands, fabric, palette, silhouette, size, condition, price ceiling (styleGuide already covers the shared sizing/shopping/reporting rules)",
      "lastRun": "YYYY-MM-DD",
      "items": [
        {
          "id": "stable id, e.g. ebay-<item-number>",
          "brand": "",
          "size": "",
          "measurements": "",
          "colorWeave": "",
          "condition": "",
          "price": "",
          "priceVerified": false,
          "source": "eBay | Etsy | Grailed | ...",
          "link": "",
          "matchNote": "why it matches (or doesn't) the criteria",
          "strongMatch": true,
          "foundAt": "YYYY-MM-DD",
          "status": "new | seen"
        }
      ]
    }
  ]
}
```

## Adding a new search

Append a new object to `searches` with a fresh `slug`, `title`, `benchmark`, and a
category-specific `criteria` (brand priorities, fabric, palette, silhouette,
sizing, condition, price ceiling — the shared sizing/shopping/reporting rules
already live in the top-level `styleGuide`, no need to repeat them). Leave
`items: []`; the next scheduled run picks it up automatically and searches every
entry in the array.

## Daily routine

A claude.ai scheduled cloud agent ("Vintage Hunt") reads `styleGuide` once for
overall aesthetic/sizing/shopping/reporting rules, then re-runs each search's own
`criteria` daily, dedupes against existing `items` by `link`, marks new finds
`"status": "new"` and previously-seen ones `"status": "seen"`, and commits
straight to `main` (safe here — this repo has no build to break). It keeps a
generous buffer per search (~40 items) and only drops a listing when it can
confirm it's actually gone, since **keep/remove decisions live client-side in the
app's browser storage, not in this file** — the routine has no visibility into
what the viewer has already triaged.

Marketplaces block automated price/detail scraping, so entries are seeded from
search results and marked `"priceVerified": false` when the price/condition
couldn't be confirmed — always check the listing before buying.
