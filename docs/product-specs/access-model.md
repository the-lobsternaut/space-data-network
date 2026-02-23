# Product Spec: Tiered Access Model

**Status**: Active
**Owner**: Web3Agent
**Last Updated**: 2026-02-23

## Overview

Lobsternaut uses a tiered access model aligned with `https://digitalarsenal.github.io/space-data-network/docs/pitchdeck.html`.

There is no usage-credit system. Capability access is determined by the active tier:
**Free**, **Explorer**, **Analyst**, **Operator**, **Mission**, **AI Enabled**.

## Capability Availability

| Tier | Price | Included Highlights |
| --- | --- | --- |
| Free | $0 | Conjunction assessment (CDMs), SGP4/SGP4-XP, high-def propagation, wallet/FIPS encryption, 3D globe |
| Explorer | $10/mo per seat | Link sharing, 10 saved scenarios, exports, custom alerts, embeds, bookmarks |
| Analyst | $20/mo per seat | 100 scenarios, Basilisk simulator, Lambert/Hohmann planning, sensor FOV, API access (25K/day) |
| Operator | $30/mo per seat | Monte Carlo, missile trajectory, launch/reentry, 500 scenarios, operator chat, CA workflow |
| Mission | $40/mo per seat | RPO/proximity ops, combat sim, EW, multi-domain, sensor fusion/fire control, unlimited scenarios |
| AI Enabled | $70 baseline (usage-based) | AI copilots, autonomous workflow automation, priority AI compute, all Mission capabilities |

Commercial terms:
- Annual billing: pay for 10 months, receive 12
- Volume discounts for 5+ seats
- AI Enabled baseline pricing: 1.75x the highest fixed tier ($40 Mission -> $70 baseline), billed by usage

## Entitlement Logic

```text
1. Resolve user's active subscription entitlement and renewal status
2. Map account to highest active tier (Free if no paid entitlement)
3. Resolve requested operation's minimum required tier
4. If user tier >= required tier: allow
5. Else: return required-tier upgrade guidance
```

## Rate Limiting

- Enforce tier-specific limits at the API gateway
- Analyst tier includes API access up to 25K/day
- Operator and Mission tiers receive higher operational limits than Analyst
- Free tier keeps strict anti-abuse limits

## Graceful Degradation

- If payment provider webhook is delayed: keep purchases in pending state and retry reconciliation
- If subscription status is temporarily unavailable: use last confirmed active tier until reconciliation completes
- If marketplace indexer lags: fall back to last confirmed ownership/receipt proofs for purchased offerings
- If tier limit is reached: return explicit tier/upgrade response, not generic auth failure
