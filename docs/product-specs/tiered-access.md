# Product Spec: Tiered Feature Access

**Status**: Active
**Owner**: Web3Agent
**Last Updated**: 2026-02-18

## Overview

OpenClaw uses a hybrid access model where features unlock based on $CLAW token holdings OR active subscriptions — whichever threshold is met.

## Access Tiers

| Property | Free | Bronze | Silver | Gold |
| --- | --- | --- | --- | --- |
| Token Hold | 0 | 10K $CLAW | 50K $CLAW | 200K $CLAW |
| OR Subscription | -- | $9.99/mo | $29.99/mo | $99.99/mo |
| API Calls/Month | 100 | 5,000 | 50,000 | Unlimited |
| Conjunction Analysis | Basic | Full CDM | Monte Carlo | Maneuver Plan |
| Mission Planning | No | No | Ground Track | Full Suite |
| Discord Access | Public | Holder | Priority | 1-on-1 |
| Early Features | No | No | 7 days | 30 days |
| Governance Votes | 0 | 1 | 5 | 20 |

## Access Paths

### Path 1: Token Holdings (Crypto-Native Users)
- Buy and hold $CLAW tokens on any supported chain
- Access is permanent as long as tokens are held
- Token balance checked on-chain via RPC
- Cached for 5 minutes to reduce RPC load

### Path 2: Stripe Subscription (Mainstream Users)
- Pay monthly with credit/debit card
- No crypto knowledge required
- Managed via Stripe Checkout hosted page
- Auto-renews monthly

### Path 3: Crypto Payment (Non-$CLAW Crypto Holders)
- Pay with BTC, ETH, USDC, SOL, MATIC
- Via Coinbase Commerce
- Same USD pricing as Stripe
- One-time or recurring

## Gating Logic

```
1. Check on-chain token balance for wallet address
2. Check database for active subscription
3. Return the HIGHER tier of the two
4. If neither meets any threshold, return 'free'
```

## Rate Limiting

Even within tier limits:
- Free: 10 req/min
- Bronze: 100 req/min
- Silver: 500 req/min
- Gold: 1000 req/min (hard cap to prevent abuse)

## Graceful Degradation

- If RPC is down: fall back to database-cached token balance (max 1 hour stale)
- If Stripe webhook is delayed: 24-hour grace period before downgrade
- If user exceeds API limit: return 429 with reset time, don't cut access entirely
