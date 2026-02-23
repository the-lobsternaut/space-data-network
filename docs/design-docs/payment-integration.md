# Design Doc: Payment Integration (Stripe + Coinbase Commerce)

**Status**: Active
**Owner**: Web3Agent
**Last Updated**: 2026-02-23

## Overview

Lobsternaut uses a tier-based subscription model for product access:
1. **Stripe (card)** — recurring per-seat subscriptions
2. **Coinbase Commerce (crypto)** — crypto checkout for tier subscriptions
3. **Token utility ($CLAW)** — discounts/marketplace utility (does not replace tier subscription)

There is no usage-credit metering in the product access model.

## Architecture

```text
User connects wallet (MetaMask/Phantom)
         │
         ├── Select tier (Free / Explorer / Analyst / Operator / Mission / AI Enabled)
         │
         ├── Pay by card?   → Stripe Checkout
         │                   └── Webhook → DB subscription record → Grant tier
         │
         └── Pay by crypto? → Coinbase Commerce
                             └── Webhook → DB subscription record → Grant tier
```

## Backend Stack

- **Server**: Node.js / Express
- **Database**: PostgreSQL
- **Auth**: SIWE (Sign-In With Ethereum) for wallet-based identity
- **ORM**: Prisma (or raw SQL if simpler for agents)

### Database Schema

```sql
CREATE TABLE users (
  id              SERIAL PRIMARY KEY,
  wallet_address  VARCHAR(64) UNIQUE NOT NULL,
  created_at      TIMESTAMP DEFAULT NOW()
);

CREATE TABLE subscriptions (
  id                    SERIAL PRIMARY KEY,
  user_id               INTEGER REFERENCES users(id),
  tier                  VARCHAR(12) NOT NULL,  -- 'free','explorer','analyst','operator','mission','ai_enabled'
  status                VARCHAR(20) NOT NULL,  -- 'active','past_due','cancelled','pending'
  payment_method        VARCHAR(20) NOT NULL,  -- 'stripe','coinbase'
  seat_count            INTEGER NOT NULL DEFAULT 1,
  stripe_customer_id    VARCHAR(64),
  stripe_sub_id         VARCHAR(64),
  coinbase_charge_id    VARCHAR(64),
  current_period_start  TIMESTAMP,
  current_period_end    TIMESTAMP,
  created_at            TIMESTAMP DEFAULT NOW(),
  updated_at            TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_subscriptions_user ON subscriptions(user_id);
CREATE INDEX idx_subscriptions_stripe ON subscriptions(stripe_customer_id);
```

## Stripe Integration

### Checkout Flow

1. User selects a tier and clicks "Subscribe with Card"
2. Backend creates Stripe Checkout Session with:
   - Price ID for selected tier
   - `client_reference_id` = wallet address
   - Success/cancel URLs
3. User completes payment in hosted Stripe checkout
4. Stripe sends `checkout.session.completed` webhook
5. Backend creates/updates subscription record
6. User tier updates immediately

### Webhook Handling

```javascript
app.post('/webhooks/stripe', express.raw({ type: 'application/json' }), (req, res) => {
  const sig = req.headers['stripe-signature'];
  const event = stripe.webhooks.constructEvent(req.body, sig, WEBHOOK_SECRET);

  switch (event.type) {
    case 'checkout.session.completed':
      // Activate selected tier
      break;
    case 'invoice.paid':
      // Renew selected tier
      break;
    case 'invoice.payment_failed':
      // Enter grace period (3 days)
      break;
    case 'customer.subscription.deleted':
      // Revert to Free tier
      break;
  }

  res.json({ received: true });
});
```

### Tier Pricing (Pitch Deck)

| Tier | Stripe Price | Billing |
| --- | --- | --- |
| Free | $0 | Forever |
| Explorer | $10/month | Per seat, recurring |
| Analyst | $20/month | Per seat, recurring |
| Operator | $30/month | Per seat, recurring |
| Mission | $40/month | Per seat, recurring |
| AI Enabled | $70 baseline (usage-based) | Usage-billed (baseline set by formula) |

Commercial defaults:
- Annual billing: pay for 10 months, receive 12
- Volume discounts for 5+ seats
- AI Enabled baseline is priced at 1.75x highest fixed tier ($40 Mission -> $70 baseline), then billed by usage

## Coinbase Commerce Integration

### Payment Flow

1. User selects tier and clicks "Pay with Crypto"
2. Backend creates Coinbase Commerce charge:
   - Amount in USD
   - Description: "Lobsternaut [Tier] Subscription"
   - `metadata.wallet_address` = user wallet
3. Coinbase checkout handles wallet payment
4. `charge:confirmed` webhook fires
5. Backend activates/renews selected tier

### Webhook Events

- `charge:created` — initiated
- `charge:confirmed` — activate/renew tier
- `charge:failed` — notify user, keep prior tier state
- `charge:expired` — timeout, clear pending state

## Security

- Stripe webhook signatures verified on every request
- Coinbase Commerce webhook signatures verified
- All payment endpoints rate-limited (10 req/min per wallet)
- Idempotent webhook handlers (duplicate deliveries handled gracefully)
- No card data stored (Stripe handles PCI)
- Database encrypted at rest

## Decision Log

| Date | Decision | Rationale |
| --- | --- | --- |
| 2026-02-14 | Stripe + Coinbase Commerce dual path | Maximizes accessibility for crypto and non-crypto users |
| 2026-02-14 | SIWE for auth | Wallet-based identity, no passwords to manage |
| 2026-02-14 | PostgreSQL | Boring, reliable, excellent tooling for agents |
| 2026-02-23 | Tier-based subscriptions (no usage credits) | Aligns product monetization with pitch-deck pricing model |
