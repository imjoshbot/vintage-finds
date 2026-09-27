# Vintage Finds

Data-only repo (public, no sensitive info — just secondhand-listing search results)
powering the "Vintage Hunt" section of the [Horowitz holiday gift guide](https://github.com/imjoshbot/2027-gift-guide).

`data/vintage-finds.json` is fetched at runtime by the gift guide's `/vintage` page
(via `raw.githubusercontent.com`, no build step needed), and is refreshed daily by
a scheduled cloud agent ("Vintage Hunt" routine).

## Schema

```jsonc
{
  "updatedAt": "ISO timestamp of the last run",
  "searches": [
    {
      "slug": "kebab-case-id",
      "title": "Human title shown as a section heading",
      "benchmark": "One-line description of the ideal/reference item",
      "criteria": "The full search brief — brands, fabric, silhouette, size, condition, price ceiling, marketplaces",
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
full `criteria` brief (write it the way you'd brief a personal shopper — brand
priorities, fabric, palette, silhouette, sizing/measurements, condition, price
ceiling, which marketplaces to check). Leave `items: []`; the next scheduled run
picks it up automatically and searches every entry in the array.

## Daily routine

A claude.ai scheduled cloud agent ("Vintage Hunt") re-runs each search's `criteria`
daily, dedupes against existing `items` by `link`, marks new finds `"status":
"new"` and previously-seen ones `"status": "seen"`, and commits straight to `main`
(safe here — this repo has no build to break). Marketplaces block automated
price/detail scraping, so entries are seeded from search results and marked
`"priceVerified": false` when the price/condition couldn't be confirmed —
always check the listing before buying.
