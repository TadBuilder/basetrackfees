# BaseTrackFees

**Fees, buybacks, burns and holder rewards across Base — built by Team TAD.**

🌐 **[Open BaseTrackFees](https://basetrackfees.com)**  
🐸 **[Follow $TAD](https://x.com/tadpolebase)**

## Why we built BaseTrackFees

Trading activity generates fees. Those fees can fund token buybacks, burns, creator revenue and distributions to holders.

Understanding those flows often means moving between token pages, platform dashboards, reward baskets and blockchain explorers. Different platforms also report their figures differently.

We created **BaseTrackFees** to bring that information into one dashboard, starting with **BaseStonk and Stonks**.

Our goal is to help the community understand where value goes, what has been distributed and which information can actually be verified.

## What the platform does

### Track buybacks and burns

Explore available buyback spending, tokens repurchased and tokens burned.

The dashboard distinguishes collected fees from buyback spending. It also separates bought-and-burned figures from total burn, so the same tokens are not counted twice.

### Explore holder rewards

View supported reward distributions with the asset name, quantity and estimated dollar value when a reliable price is available.

Depending on the project, rewards can include the project's own token, other crypto assets or tokenised stocks. Supported Stockify baskets include their available composition and payout information.

Configured basket weights and recorded payouts are treated as different pieces of information.

### Search tokens and wallets

Browse the BaseStonk and Stonks catalogues using search, filters, sorting and pagination.

Token views bring together available statistics, artwork, social links, platform links and contract addresses.

Wallet searches retrieve reward information from supported sources. For supported Stonks distributions, transaction checks help identify recorded payments to the searched address. Coverage varies by project and source.

### Compare platforms

Switch between BaseStonk and Stonks within the same application, with consistent navigation and platform-specific presentation.

**O1.exchange is planned and currently marked "Coming soon."**

## The technology behind it

BaseTrackFees combines several types of sources:

- Platform APIs for token catalogues, market information and reported statistics.
- Public reward pages and data feeds for distribution information.
- Read-only blockchain queries for contract configuration and token metadata.
- Explorer records for supported transfer and transaction checks.

The application brings these sources into a shared data model while preserving differences in units, availability and coverage.

## APIs and sources used

The following endpoints describe the current implementation. Availability and response formats are controlled by the source providers.

### BaseStonk

Origin: [api.basestonk.io](https://api.basestonk.io)

| Endpoint | Purpose |
|---|---|
| `/api/launchpad/tokens` | Paginated token catalogue. |
| `/api/launchpad/tokens/{token}` | Token details and available statistics. |
| `/api/launchpad/tokens/{token}/trades` | Recent trading activity. |
| `/api/launchpad/pairs/base` | Paired-asset information. |
| `/api/launchpad/stats/base` | Platform statistics. |
| `/api/launchpad/burn/base` | Platform burn totals. |
| `/api/launchpad/burn/base/history` | Available burn history. |
| `/api/launchpad/basket/rounds` | Basket distribution rounds. |
| `/api/launchpad/rewards/{wallet}` | Wallet reward summary. |
| `/api/launchpad/tokens/{token}/rewards/{wallet}` | Token-specific wallet rewards. |

Requests use chain, pagination and sorting parameters where applicable. Token and wallet placeholders refer to their contract or wallet addresses.

### Stonks Exchange

Origin: [www.thestonks.exchange](https://www.thestonks.exchange)

| Endpoint | Purpose |
|---|---|
| `/api/coins` | Token catalogue and available metadata. |
| `/api/dex-prices?addrs=…` | Available prices, FDV, liquidity and trading metrics. |
| `/api/analytics` | Platform analytics and available historical series. |
| `/api/stonkex` | STONKEX burn information and buyback/burn events. |

### Stockify

Origin: [www.stockify.finance](https://www.stockify.finance)

The tracker reads public pages at `/indices/all` and `/indices/{index}` for available index fees, payouts, burns and reward composition.

This integration parses published pages rather than using a dedicated statistics API. Layout changes can temporarily affect coverage.

### Additional sources

| Source | Endpoint or public resource | Role |
|---|---|---|
| [Prime Stonks](https://primestonks.fund) | `/data/treasury.json` | Additional distribution records for DGUY's separate reward system. |
| [DEX Screener](https://api.dexscreener.com) | `/tokens/v1/base/{addresses}` | Available token artwork and banner metadata. |
| [BaseScan](https://basescan.org) | Public token-transfer and transaction pages | Transfer discovery and transaction-log checks for supported payouts. |
| [Telegram Bot API](https://api.telegram.org) | Bot API methods | Delivery of statistics through the connected Telegram bot. |

## Blockchain reads

The application uses Base RPC providers:

- [base-rpc.publicnode.com](https://base-rpc.publicnode.com)
- [base.drpc.org](https://base.drpc.org)

**Viem** handles contract encoding and decoding. **Multicall3** groups compatible read requests to reduce individual calls.

Contract reads help resolve token decimals, supply, pool fees, reward-index configuration and basket assets. These are read-only operations.

For supported Stonks wallet payouts, the implementation checks successful transaction records and raw event data obtained from BaseScan. It matches relevant reward contracts, recipient addresses and transferred assets. DGUY attribution additionally relies on the project's published distribution ledger.

These checks do not establish complete coverage of every wallet or token.

## Application stack

| Layer | Technology |
|---|---|
| Application | TypeScript and React |
| Framework and build | Vinext and Vite |
| Styling | Tailwind CSS |
| Charts | Recharts |
| Server runtime | Cloudflare Workers |
| Persistent storage | Cloudflare D1 |
| Blockchain interaction | Viem and Multicall3 |

## Updates and reliability

An external hourly schedule collects and saves platform snapshots. The website reads those saved records, while some detail views retrieve additional information on demand.

Pagination, caching, bounded parallel requests and shared in-flight requests reduce repeated work. Previously saved figures remain available if a collection fails; incomplete sources may also be reported with warnings or retained timestamps.

The interface retains loaded data during supported navigation and refresh operations to reduce unnecessary loading interruptions.

A snapshot records the tracker's collection time. It does not guarantee that every upstream source was updated at that exact moment.

## How to interpret the figures

- **Unavailable is not zero.** Missing information remains identified as unavailable.
- **Dollar values can be estimates.** Conversions use available prices and may differ from the value when a payment occurred.
- **Market cap and FDV have different meanings.** Some valuations are estimates or use an identified FDV fallback when the preferred figure is unavailable.
- **Collected fees are not buyback spending.** They describe different stages of the flow.
- **Bought-and-burned tokens are included in total burn.** Adding both would double-count them.
- **Wallet coverage is source-dependent.** A result does not necessarily represent a wallet's complete reward history.
- **Source freshness matters.** Snapshot timestamps and source errors help explain what a figure represents.

BaseTrackFees organises and checks available information; it does not claim that every upstream source is complete.

## Built by Team TAD

We created BaseTrackFees to contribute practical tools to the Base ecosystem and support communities around platforms such as BaseStonk, Stonks and, in the future, O1.exchange.

The project brings together data collection, blockchain reads, reward tracking and a shared interface around one purpose: making these flows easier to follow.

AI tools assisted parts of development.

BaseTrackFees is an independent community project and is not an official product of the platforms it tracks.

**[Explore BaseTrackFees](https://basetrackfees.com)** · **[Follow Team TAD](https://x.com/tadpolebase)**
