# facebook-ublock-filters

[![Version](https://img.shields.io/badge/version-2026.10.29.2-blue)](CHANGELOG.md) [![License: MIT](https://img.shields.io/badge/license-MIT-green)](LICENSE) [![Platform](https://img.shields.io/badge/uBlock%20Origin-Firefox%20%7C%20Chromium-orange)](https://github.com/gorhill/uBlock)

A uBlock Origin filter list that takes the ads out of the Facebook news feed, the right column, Marketplace and search results.

uBO's stock lists still carry Facebook rules, but as of late October 2026 they let feed ads through. One of those rules even trips Facebook's anti-adblock check, which is what takes every other Facebook rule down with it. This list turns that check off and disables the stock rule that trips it. Then it removes the ads in the feed, the right column, Marketplace and search.

## How it works

Facebook doesn't label ads in the page markup any more, so cosmetic filters can't find them. It still marks each ad inside its own data: a feed ad carries a `th_dat_spo` object (the renamed `sponsored_data`) and a `CometStorySponsoredLabelStrategy` section that draws the "Sponsored" tag. Marketplace ads are `MarketplaceFeedAdStory` objects. The rules use uBO's `json-edit` scriptlet to delete those from the JSON before Facebook's code draws anything, so there are no blank boxes left behind.

Facebook fights this with code it calls GHL. It fetches feed data with a stock `XMLHttpRequest` that skips uBO's hooks. Before trusting `JSON.parse`, it runs a decoy through it, `{"node":{"s":{"__typename":"SponsoredData"}}}`. If the decoy comes back damaged, Facebook stops using `JSON.parse` and switches to its own JavaScript parser, which no filter can reach. uBO's stock rule `json-edit, ..node[?.*.__typename=="SponsoredData"]` damages exactly that decoy. So this list carries an exception for that one rule. It also prunes GHL's switches out of the page's `envjson` block, so the first page load parses through `JSON.parse` too.

## Install

Subscribe to it so uBlock Origin picks up fixes on its own (it checks once a day):

1. In uBlock Origin, open the dashboard and go to **Filter lists**.
2. Scroll to the bottom, tick **Import...** and paste this URL into the box:
   ```
   https://raw.githubusercontent.com/SysAdminDoc/facebook-ublock-filters/main/facebook-ads.txt
   ```
3. Click **Apply changes** and reload Facebook.

If you'd rather update by hand, copy everything in [facebook-ads.txt](facebook-ads.txt) and paste it at the end of **My filters** instead. Don't do both.

It needs full uBlock Origin (Firefox, or a Chromium browser that still runs it, like Brave). uBO Lite can't run these scriptlets. None of the rules need the "Allow custom filters requiring trust" box, so leave it however you have it.

Pasted an older copy into My filters before? Delete that Facebook Ads block first, then subscribe.

## Verify

Open the Facebook home page, scroll a few screens and look for the Sponsored tag. There shouldn't be one, and the right column's Sponsored box should be gone. On Marketplace, ad tiles show a "Learn more" or "Shop now" button; those should be gone too. The uBO badge counts network blocks, not these scriptlets, so it may read zero.

If ads show up on the very first Facebook load right after the browser starts, reload once. uBO's scriptlets can miss a page that loads before uBO has finished starting.

If ads come back for good, either Facebook renamed a field or it changed GHL. Open the network panel, find an `/api/graphql/` response holding a sponsored story and look for the new marker next to `__typename`. If the marker is still `th_dat_spo`, search Facebook's scripts for `ad_blocker_defense` and check whether the decoy or the `envjson` switch names changed.

## Credits

Field names and the GHL behavior were checked against a live account on October 29, 2026, in Brave with uBlock Origin 1.75.0 (13 of 13 served feed ads removed). The scriptlets are [uBlock Origin's](https://github.com/gorhill/uBlock/wiki/Resources-Library).
