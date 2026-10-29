# facebook-ublock-filters

[![Version](https://img.shields.io/badge/version-2026.10.29.1-blue)](CHANGELOG.md) [![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE) [![Platform](https://img.shields.io/badge/uBlock%20Origin-Firefox%20%7C%20Chromium-orange)](https://github.com/gorhill/uBlock)

A uBlock Origin filter list that takes the ads out of the Facebook news feed, the right column, Marketplace and search results.

uBO's own "Quick fixes" list still carries some Facebook rules and catches most feed ads. It has nothing for Marketplace, though, and it leans on one field name. This list adds Marketplace and later pages of search results. It also matches feed ads on a second marker, so one rename on Facebook's side won't bring them all back.

## How it works

Facebook doesn't label ads in the page markup any more, so cosmetic filters can't find them. It still marks each ad inside its own data: a feed ad carries a `th_dat_spo` object (the renamed `sponsored_data`) and a `CometStorySponsoredLabelStrategy` section that draws the "Sponsored" tag. Marketplace ads are `MarketplaceFeedAdStory` objects. The rules use uBO's `json-edit` and `jsonl-edit-*` scriptlets to delete those from the JSON before Facebook's code draws anything, so there are no blank boxes left behind.

## Install

This repo is private, so uBlock Origin can't subscribe to it by URL. Paste it in instead:

1. Open [facebook-ads.txt](facebook-ads.txt) and copy everything.
2. In uBlock Origin, open the dashboard and go to **My filters**.
3. Paste at the end, click **Apply changes**, reload Facebook.

It needs full uBlock Origin (Firefox, or a Chromium browser that still runs it). uBO Lite can't run these scriptlets from My filters.

The `trusted-prevent-dom-bypass` lines at the bottom only run from My filters. They stop Facebook from grabbing an unhooked `JSON.parse` or `XMLHttpRequest` out of a throwaway iframe.

## Verify

Open the Facebook home page, scroll a few screens and look for the Sponsored tag. There shouldn't be one, and the right column's Sponsored box should be gone. On Marketplace, ad tiles show a "Learn more" or "Shop now" button; those should be gone too. The uBO badge counts network blocks, not these scriptlets, so it may read zero.

If ads show up on the very first Facebook load right after the browser starts, reload once. uBO's scriptlets can miss a page that loads before uBO has finished starting.

If ads come back for good, Facebook renamed a field. Open the network panel, find an `/api/graphql/` response holding a sponsored story and look for the new marker next to `__typename`.

## Credits

Field names were checked against a live account on October 29, 2026. The scriptlets are [uBlock Origin's](https://github.com/gorhill/uBlock/wiki/Resources-Library).
