# Design Doc: Multi-Chain Token Strategy

**Status**: Active
**Owner**: Web3Agent
**Last Updated**: 2026-02-18

## Overview

$CLAW is deployed across Base, Solana, and Ethereum as separate tokens with the same ticker. Bitcoin address accepts BTC donations. No bridging initially.

## Tokenomics

| Property | Value |
| --- | --- |
| Ticker | $CLAW |
| Total Supply | 1,000,000,000 (1B across all chains) |
| Burn on Transfer | 0.5% |
| Burn on API Redemption | 5% |
| Mint Function | None (fixed supply) |

### Distribution

| Allocation | Percentage | Tokens | Vesting |
| --- | --- | --- | --- |
| Public Sale | 40% | 400M | None |
| Liquidity Pools | 25% | 250M | Locked 6 months |
| Development Treasury | 20% | 200M | 12-month linear unlock |
| Team | 10% | 100M | 24-month cliff + linear |
| Community Rewards | 5% | 50M | Distributed over 24 months |

## Chain Deployment Order

### 1. Base (Priority — Week 1)
- **Why first**: Low fees, active AI agent community, fast deployment via Bankr/Clanker
- **Standard**: ERC-20
- **Deploy method**: Bankr bot (similar to KellyClaude) for immediate liquidity
- **Initial liquidity**: $5K-$10K in ETH paired with $CLAW
- **DEX**: Uniswap (Base) or Aerodrome

### 2. Solana (Phase 2 — Weeks 5-12)
- **Why second**: High-frequency API usage, microtransactions, large crypto community
- **Standard**: Token-2022 (native transfer fee support)
- **Deploy method**: Standard SPL token creation
- **Initial liquidity**: $5K-$10K in SOL paired with $CLAW
- **DEX**: Jupiter

### 3. Ethereum (Phase 3 — Weeks 13-16)
- **Why third**: Institutional credibility, DeFi composability, highest gas costs
- **Standard**: ERC-20 (OpenZeppelin)
- **Deploy method**: Standard contract deployment
- **Initial liquidity**: $10K-$20K in ETH paired with $CLAW
- **DEX**: Uniswap V3

### 4. Bitcoin (Ongoing)
- **Purpose**: Donations and long-term value store
- **Method**: Native BTC address
- **Future**: Evaluate Ordinals/Runes integration based on demand

## Cross-Chain Strategy

Separate tokens per chain. No bridging.

**Rationale**:
- Simpler to deploy and manage
- Independent communities per ecosystem
- No bridge security risks (bridges are the #1 DeFi attack vector)
- Users can swap via DEXes within each chain

**Re-evaluate bridging at**: 1,000+ holders across chains, if there's strong demand

## Listing Targets

| Platform | Type | Timeline |
| --- | --- | --- |
| DexScreener | Aggregator | Day 1 (automatic for DEX tokens) |
| CoinGecko | Aggregator | Month 2 (requires application) |
| CoinMarketCap | Aggregator | Month 3 (requires application + volume) |
| Jupiter | Solana DEX | After Solana token deployment |
| Uniswap | Ethereum DEX | After Ethereum token deployment |

## Decision Log

| Date | Decision | Rationale |
| --- | --- | --- |
| 2026-02-14 | Separate tokens over bridged | Security, simplicity, independent communities |
| 2026-02-14 | Base first | Low fees, AI agent ecosystem, fast Bankr deploy |
| 2026-02-14 | Fixed supply, no mint | Trust and deflationary pressure |
| 2026-02-14 | 0.5% transfer burn | Mild deflation without punishing traders |
