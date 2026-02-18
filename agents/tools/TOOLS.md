# Agent Tools Configuration

> Available tools, API integrations, and service configurations.
> API keys stored in environment variables, NEVER in this file.
> This file documents WHAT tools exist and HOW to use them.

## Development Tools

| Tool | Purpose | Config |
| --- | --- | --- |
| GitHub CLI (`gh`) | PR management, issue tracking | Auth via `gh auth login` |
| CMake | C++ build system | `CMakeLists.txt` in OrbPro root |
| Emscripten (`emcc`) | C++ to WASM compilation | Install via `emsdk` |
| Google Test | C++ unit testing | Linked via CMake |
| Playwright | Browser testing for WASM | `npm install playwright` |
| Doxygen | C++ API doc generation | `Doxyfile` in OrbPro root |

## Content Tools

| Tool | Purpose | Config | Env Var |
| --- | --- | --- | --- |
| OpenAI API | Image generation (gpt-image-1.5) | platform.openai.com | `OPENAI_API_KEY` |
| Postiz | TikTok posting + analytics | postiz.pro | `POSTIZ_API_KEY`, `POSTIZ_INTEGRATION_ID` |

## Payment Tools

| Tool | Purpose | Config | Env Var |
| --- | --- | --- | --- |
| Stripe | Fiat subscriptions | dashboard.stripe.com | `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET` |
| Coinbase Commerce | Crypto payments | commerce.coinbase.com | `COINBASE_COMMERCE_API_KEY`, `COINBASE_WEBHOOK_SECRET` |

## Blockchain Tools

| Tool | Purpose | Config | Env Var |
| --- | --- | --- | --- |
| Alchemy / Infura | Base + Ethereum RPC | alchemy.com / infura.io | `BASE_RPC_URL`, `ETH_RPC_URL` |
| Helius / QuickNode | Solana RPC | helius.dev / quicknode.com | `SOLANA_RPC_URL` |
| Hardhat / Foundry | Smart contract dev + testing | Local install | N/A |
| OpenZeppelin | ERC-20 contract library | npm package | N/A |

## Analytics Tools

| Tool | Purpose | Config | Env Var |
| --- | --- | --- | --- |
| RevenueCat | Subscription analytics (if mobile app) | revenuecat.com | `REVENUECAT_API_KEY` |
| DexScreener | Token price/volume tracking | dexscreener.com | Public API (no key) |
| TikTok Analytics | Content performance | Via Postiz API | (uses Postiz key) |

## Community Tools

| Tool | Purpose | Config |
| --- | --- | --- |
| Discord Bot | Server management, token-gating | `DISCORD_BOT_TOKEN` |
| Collab.Land | Token-gated roles in Discord | collab.land integration |

## Memory Tools

> See `agents/skills/memory-management.md` for full configuration details.

| Tool | Purpose | Status | Notes |
| --- | --- | --- | --- |
| Built-in memory flush | Saves context before compaction | **Configure first** | Most impactful single change |
| Built-in hybrid search | Vector + BM25 retrieval | **Configure first** | Catches exact + conceptual matches |
| QMD | Superior retrieval backend (BM25 + vectors + reranking) | Evaluate | By @tobi (Shopify CEO), indexes external docs |
| Mem0 | Compaction-proof external memory | Evaluate | Auto-capture + auto-recall, YC-backed |
| Cognee | Knowledge graph from data | Evaluate later | Entity relationships, requires Docker |
| Obsidian | External brain, cross-device review | Evaluate | Symlink or QMD index approach |

## Environment Variable Template

Create a `.env` file (NEVER commit this):

```bash
# OpenAI
OPENAI_API_KEY=sk-...

# Postiz (TikTok)
POSTIZ_API_KEY=...
POSTIZ_INTEGRATION_ID=...

# Stripe
STRIPE_SECRET_KEY=sk_live_...
STRIPE_PUBLISHABLE_KEY=pk_live_...
STRIPE_WEBHOOK_SECRET=whsec_...

# Coinbase Commerce
COINBASE_COMMERCE_API_KEY=...
COINBASE_WEBHOOK_SECRET=...

# Blockchain RPCs
BASE_RPC_URL=https://base-mainnet.g.alchemy.com/v2/...
ETH_RPC_URL=https://eth-mainnet.g.alchemy.com/v2/...
SOLANA_RPC_URL=https://mainnet.helius-rpc.com/?api-key=...

# Discord
DISCORD_BOT_TOKEN=...

# Database
DATABASE_URL=postgresql://user:pass@host:5432/openclaw

# RevenueCat (optional)
REVENUECAT_API_KEY=...
```
