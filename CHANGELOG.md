# Changelog

## Unreleased

- Fixed feed ads still showing in Chromium browsers. Facebook's anti-adblock check (GHL) feeds a decoy through `JSON.parse`, and uBO's stock `..node[?.*.__typename=="SponsoredData"]` rule damaged it, so Facebook moved its feed parsing to a parser no filter reaches. The list now turns off that one stock rule and prunes GHL's switches from the page's `envjson` block.
- Dropped the `trusted-prevent-dom-bypass` lines. Facebook now loads its helper iframe at `about:blank#g`, which that scriptlet skips, and the lines needed the trust checkbox anyway. Nothing in the list needs trust now.

## 2026.10.29.1

- First list. Removes feed ads (`th_dat_spo`, `CometStorySponsoredLabelStrategy`), the right-column Sponsored box (`AdsSideFeedUnit`), Marketplace ad tiles wherever they sit in the data (`MarketplaceFeedAdStory`, including `upsell_stories`) and search result ads (`SEARCH_ADS`).
- Keeps a rule for the old `sponsored_data` field in case Facebook flips back.
- Blocks the iframe trick Facebook uses to fetch an unhooked `JSON.parse`.
