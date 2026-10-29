# Changelog

## Unreleased

## 2026.10.29.1

- First list. Removes feed ads (`th_dat_spo`, `CometStorySponsoredLabelStrategy`), the right-column Sponsored box (`AdsSideFeedUnit`), Marketplace ad tiles wherever they sit in the data (`MarketplaceFeedAdStory`, including `upsell_stories`) and search result ads (`SEARCH_ADS`).
- Keeps a rule for the old `sponsored_data` field in case Facebook flips back.
- Blocks the iframe trick Facebook uses to fetch an unhooked `JSON.parse`.
