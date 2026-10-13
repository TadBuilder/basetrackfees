# BaseTrack Scout Assistant — Chrome beta 0.1.9

14 October 2026 · Built by [Based Scout](https://x.com/ScoutOnBase)

BaseTrack Scout Assistant brings BaseStonk and Stonks into a Chrome side panel. It opens on new coins, lets you search by name, ticker or contract address, and keeps public coin stats within reach while browsing.

**Version 0.1.9 has been submitted to the Chrome Web Store. It is awaiting review, with automatic publication enabled after approval.** The store installation link will be added once it is available.

[BaseTrackFees](https://basetrackfees.com) · [Based Scout on X](https://x.com/ScoutOnBase) · [Privacy policy](https://basetrackfees.com/privacy/companion)

## What it does

- Browse new coins from BaseStonk and Stonks, filtered by platform and the last 24 hours, 3 days or 7 days.
- Search the tracked catalog by ticker or name. A contract address is optional.
- Open available price, market cap or FDV, liquidity, 24-hour volume, fees, burns and holder rewards. Available fields depend on the coin and source.
- Pin coins and view recorded price charts over 24 hours, 7 days or 30 days.
- Enable launch notifications and configure price, valuation or volume alerts.
- Show a translucent alert at the bottom right of the current website, with the coin’s logo, name and a Chart button. The button opens the coin page on its source platform.
- Inspect a supported token page or selected contract address through the extension’s controls.
- Switch between light and dark themes.

The website alert has no social link or mascot. It closes automatically after eight seconds, pauses while hovered or focused, and falls back to initials if the coin logo fails to load. A small BaseTrackFees link remains in the side panel.

## APIs and data sources

These are the public endpoints used by the extension. `{CA}` is a token contract address; `{platform}` is `basestonk` or `stonks`. Endpoint availability and response formats are controlled by their providers.

### BaseTrackFees

Origin: [basetrackfees.com](https://basetrackfees.com)

| Endpoint | Used for |
| --- | --- |
| `/api/monitor` | Saved BaseStonk catalog, market fields and available cumulative activity. |
| `/api/stonks` | Saved Stonks catalog, market fields and available cumulative activity. |
| `/api/market/signals?platform=stonks` | Recorded Stonks launch information. |
| `/api/market/history?coin={platform}:{CA}&range={range}` | Recorded USD price history for `24h`, `7d` or `30d`. |

### BaseStonk

Origin: [api.basestonk.io](https://api.basestonk.io)

| Endpoint | Used for |
| --- | --- |
| `/api/launchpad/tokens?chain=base&limit=100&offset=0&sort=age` | Direct checks of the latest Base-chain listings. |
| `/api/v1/launchpad/tokens/{CA}?chain=base` | Recovery of missing or older token market fields. |
| `/api/launchpad/tokens/{CA}/candles?chain=base&interval={interval}&limit={limit}` | Candle history fallback when site history is unavailable or retained. The extension uses `5m` or `1d` intervals. |

### Stonks Exchange

Origin: [www.thestonks.exchange](https://www.thestonks.exchange)

| Endpoint | Used for |
| --- | --- |
| `/api/coins` | Direct listing checks and available launch timestamps. |
| `/api/coin/{CA}/market` | Token market data, quote-asset conversion, volume and trade count. |
| `/api/dex-prices?addrs={CA}` | Available USD price, FDV, liquidity and price-change data. |

The extension reads these launchpad endpoints directly for recent listings and market recovery. Its broader searchable catalog and cumulative activity come through BaseTrackFees. Public token logos load from the image URLs supplied by the data sources.

## Refresh behaviour

| Data | Target interval while Chrome runs |
| --- | --- |
| Direct launch feed with the panel visible | 1 minute |
| Direct launch feed in the background | 2 minutes |
| Full BaseTrackFees catalogs | 5 minutes |
| Viewed-coin market recovery cache | 2 minutes |
| Chart cache | 5 minutes |

The first successful notification scan establishes a baseline so old launches are not announced again. Later alerts use verified launch times or new threshold crossings. Unknown launch dates are labelled **Newly tracked**. Repeated market signals have a six-hour cooldown.

This is periodic API polling, not a transaction or mint-event subscription. Device sleep, provider delays, rate limits and Chrome restrictions can delay checks.

## Browser implementation

The extension uses Manifest V3, packaged JavaScript modules, HTML and CSS.

- `chrome.sidePanel` displays the interface.
- `chrome.storage.local` keeps preferences, pinned coins and bounded public-data caches; `chrome.storage.session` keeps temporary inspection state.
- `chrome.alarms` schedules background checks.
- `chrome.notifications` delivers optional desktop alerts.
- `chrome.permissions` requests optional notification and website access when enabled.
- The `activeTab` permission, `chrome.scripting` and `chrome.contextMenus` support user-requested page inspection and in-page alerts.

No wallet connection, private key, trading permission or user API key is required. In-page alerts render in an isolated Shadow DOM and do not read the page’s text or forms.

## Data quality and validation

Source dates stay visible. Failed requests retain the latest available recording with its original timestamp. Missing values remain unavailable; confirmed zero values stay zero. Market cap, FDV and estimates are labelled separately, and missing candles are not fabricated.

**84 automated tests pass**, covering notification deduplication, launch baselines, provider failures, stat recovery, chart validation, search and panel interactions. The production build also completed successfully. These checks do not guarantee the availability of every upstream metric or a fixed notification delivery time.

BaseTrack Scout Assistant is an independent community tool for the Base ecosystem. It is not an official extension of Base, BaseStonk, Stonks or Google.
