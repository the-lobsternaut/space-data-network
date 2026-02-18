# Design Doc: Payment Integration (Stripe + Coinbase Commerce)

**Status**: Active
**Owner**: Web3Agent
**Last Updated**: 2026-02-18

## Overview

OpenClaw offers three payment paths to maximize accessibility:
1. **Token holdings** — buy and hold $CLAW (permanent access while holding)
2. **Credit card** — Stripe subscription (monthly, no crypto knowledge needed)
3. **Crypto payment** — Coinbase Commerce (pay with BTC/ETH/USDC, no $CLAW needed)

## Architecture

```
User connects wallet (MetaMask/Phantom)
         │
         ├── Has enough $CLAW tokens? → Grant tier based on balance
         │
         ├── Wants to pay with card? → Stripe Checkout
         │   └── Webhook → Database → Grant tier
         │
         └── Wants to pay with crypto? → Coinbase Commerce
             └── Webhook → Database → Grant tier
```

## Backend Stack

- **Server**: Node.js / Express
- **Database**: PostgreSQL
- **Auth**: SIWE (Sign-In With Ethereum) for wallet-based identity
- **ORM**: Prisma (or raw SQL if simpler for agents)

### Database Schema

```sql
CREATE TABLE users (
  id            SERIAL PRIMARY KEY,
  wallet_address VARCHAR(64) UNIQUE NOT NULL,
  created_at    TIMESTAMP DEFAULT NOW()
);

CREATE TABLE subscriptions (
  id                SERIAL PRIMARY KEY,
  user_id           INTEGER REFERENCES users(id),
  tier              VARCHAR(10) NOT NULL,  -- 'bronze', 'silver', 'gold'
  status            VARCHAR(20) NOT NULL,  -- 'active', 'past_due', 'cancelled'
  payment_method    VARCHAR(20) NOT NULL,  -- 'stripe', 'coinbase'
  stripe_customer_id VARCHAR(64),
  stripe_sub_id     VARCHAR(64),
  coinbase_charge_id VARCHAR(64),
  current_period_start TIMESTAMP,
  current_period_end   TIMESTAMP,
  created_at        TIMESTAMP DEFAULT NOW(),
  updated_at        TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_subscriptions_wallet ON subscriptions(user_id);
CREATE INDEX idx_subscriptions_stripe ON subscriptions(stripe_customer_id);
```

## Stripe Integration

### Checkout Flow

1. User clicks "Subscribe with Card"
2. Backend creates Stripe Checkout Session with:
   - Price ID for the selected tier
   - `client_reference_id` = wallet address
   - Success/cancel URLs
3. User completes payment on Stripe's hosted page
4. Stripe sends `checkout.session.completed` webhook
5. Backend creates subscription record in database
6. User's access tier updates immediately

### Webhook Handling

```javascript
app.post('/webhooks/stripe', express.raw({type: 'application/json'}), (req, res) => {
  const sig = req.headers['stripe-signature'];
  const event = stripe.webhooks.constructEvent(req.body, sig, WEBHOOK_SECRET);

  switch (event.type) {
    case 'checkout.session.completed':
      // Create subscription
      break;
    case 'invoice.paid':
      // Renew subscription
      break;
    case 'invoice.payment_failed':
      // Enter grace period (3 days)
      break;
    case 'customer.subscription.deleted':
      // Deactivate subscription
      break;
  }

  res.json({ received: true });
});
```

### Pricing Configuration

| Tier | Stripe Price | Billing |
| --- | --- | --- |
| Bronze | $9.99/month | Monthly recurring |
| Silver | $29.99/month | Monthly recurring |
| Gold | $99.99/month | Monthly recurring |

## Coinbase Commerce Integration

### Payment Flow

1. User clicks "Pay with Crypto"
2. Backend creates Coinbase Commerce Charge:
   - Amount in USD
   - Description: "OpenClaw [Tier] Subscription"
   - `metadata.wallet_address` = user's wallet
3. Coinbase Commerce modal shows payment options (BTC, ETH, USDC, etc.)
4. User sends crypto from any wallet
5. `charge:confirmed` webhook fires
6. Backend creates subscription record

### Webhook Events

- `charge:created` — Payment initiated (no action needed)
- `charge:confirmed` — Payment confirmed on blockchain → activate subscription
- `charge:failed` — Payment failed → notify user
- `charge:expired` — 60-minute timeout → clean up pending record

## Security

- Stripe webhook signatures verified on every request
- Coinbase Commerce webhook signatures verified
- All payment endpoints rate-limited (10 req/min per wallet)
- Idempotent webhook handlers (duplicate deliveries handled gracefully)
- No credit card data stored (Stripe handles PCI)
- Database encrypted at rest

## Decision Log

| Date | Decision | Rationale |
| --- | --- | --- |
| 2026-02-14 | Stripe + Coinbase Commerce dual path | Maximizes accessibility for crypto and non-crypto users |
| 2026-02-14 | SIWE for auth | Wallet-based identity, no passwords to manage |
| 2026-02-14 | PostgreSQL | Boring, reliable, excellent tooling for agents |
| 2026-02-14 | Token balance takes priority over subscription | Incentivizes token holding (one-time vs recurring) |
