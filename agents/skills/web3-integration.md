# Web3 Integration Skill — Rules and Patterns

> Token deployment, payment integration, and feature gating rules.
> ~200 lines initial seed. Grows as deployment experiences accumulate.

## Core Rules

### R-001: Never Commit Secrets

NEVER commit to the repository:
- Private keys or seed phrases
- API keys (Stripe, Coinbase Commerce, RPC endpoints)
- Webhook signing secrets
- Database credentials
- Any string that looks like a key, token, or password

Use environment variables. Use `.env.example` with placeholder values. Add `.env` to `.gitignore`.

If a secret is accidentally committed:
1. Rotate the secret IMMEDIATELY (don't just remove it from the repo — git history preserves it)
2. Force-push to remove from history (only time force-push is acceptable)
3. Log the incident to `tasks/lessons.md`

### R-002: Testnet Before Mainnet, Always

Every smart contract and token deployment follows this order:

1. Local development (Hardhat/Foundry local node)
2. Testnet deployment (Base Sepolia, Solana Devnet, Goerli/Sepolia)
3. Testnet verification and testing (minimum 48 hours)
4. Mainnet deployment
5. Mainnet verification

Never skip testnet. Never "just deploy to mainnet real quick."

### R-003: Token Deployment Checklist — Base (ERC-20)

- [ ] Contract inherits OpenZeppelin ERC-20 (don't roll your own)
- [ ] Total supply: 1,000,000,000 (1B) minted to deployer
- [ ] Transfer fee: 0.5% burn on every transfer (configurable by owner)
- [ ] API credit redemption: 5% burn
- [ ] Owner functions: pause, unpause, update fee (with timelock)
- [ ] No mint function (fixed supply)
- [ ] Deployed via Bankr bot or Clanker for immediate liquidity
- [ ] Verified on BaseScan
- [ ] Liquidity added to Uniswap (Base) or Aerodrome
- [ ] Listed on DexScreener

### R-004: Token Deployment Checklist — Solana (Token-2022)

- [ ] Use Token-2022 program (supports transfer fees natively)
- [ ] Transfer fee: 0.5% (configured at mint creation)
- [ ] Mint authority: revoked after initial mint (or held by multisig)
- [ ] Freeze authority: revoked (tokens should be freely transferable)
- [ ] Metadata: set via Metaplex token metadata standard
- [ ] Listed on Jupiter
- [ ] Listed on DexScreener

### R-005: Token Deployment Checklist — Ethereum (ERC-20)

- [ ] Contract inherits OpenZeppelin ERC-20
- [ ] Same tokenomics as Base deployment
- [ ] Deployed via standard contract deployment (not bot — gas is too high)
- [ ] Verified on Etherscan
- [ ] Liquidity added to Uniswap V3
- [ ] Audit completed before mainnet (at minimum: Slither static analysis)

### R-006: Stripe Integration Rules

**Webhook security**:
- Always verify webhook signatures using `stripe.webhooks.constructEvent()`
- Never trust unverified webhook payloads
- Use idempotency keys for all API calls
- Handle duplicate webhook deliveries gracefully (idempotent handlers)

**Subscription lifecycle**:
```
checkout.session.completed → Create subscription record
invoice.paid → Renew/confirm subscription
invoice.payment_failed → Enter 3-day grace period
customer.subscription.deleted → Deactivate access
customer.subscription.updated → Update tier
```

**Database schema**:
- Store wallet_address + subscription_tier + stripe_customer_id + status
- Index on wallet_address (primary lookup path)
- Index on stripe_customer_id (webhook lookup path)
- Never store full credit card details (Stripe handles PCI compliance)

### R-007: Coinbase Commerce Integration Rules

- Create charges via Coinbase Commerce API
- Support: BTC, ETH, USDC, USDT, SOL, MATIC
- Webhook: `charge:confirmed` triggers subscription activation
- Handle `charge:failed` and `charge:pending` states
- Same pricing as Stripe tiers (USD equivalent)
- Charges expire after 60 minutes — handle `charge:expired`

### R-008: Access Control Logic

The hybrid gating function checks token balance first, then subscription:

```javascript
async function getAccessTier(walletAddress) {
  const [tokenBalance, subscription] = await Promise.all([
    getTokenBalance(walletAddress),
    getSubscription(walletAddress)
  ]);

  // Token balance takes priority (one-time purchase, permanent)
  if (tokenBalance >= 200_000) return 'gold';
  if (tokenBalance >= 50_000) return 'silver';
  if (tokenBalance >= 10_000) return 'bronze';

  // Subscription fallback (monthly payment)
  if (subscription?.status === 'active') return subscription.tier;

  return 'free';
}
```

Rules:
- Token balance is checked on-chain (not cached) for tier determination
- Cache the result for 5 minutes to avoid excessive RPC calls
- Subscription status is checked in the database
- If both exist, the HIGHER tier wins
- Rate limit: even Gold tier gets 1000 req/sec max (prevent abuse)

### R-009: Smart Contract Security Checklist

Before any mainnet deployment:
- [ ] Static analysis with Slither (zero high/medium findings)
- [ ] No reentrancy vulnerabilities
- [ ] No integer overflow/underflow (use SafeMath or Solidity 0.8+)
- [ ] Owner functions have reasonable access controls
- [ ] No selfdestruct
- [ ] No delegatecall to untrusted contracts
- [ ] Transfer fees are bounded (can't be set to 100%)
- [ ] Emergency pause function exists
- [ ] Timelock on critical parameter changes (min 24 hours)

### R-010: Wallet Address Validation

Always validate wallet addresses before processing:

```javascript
// Ethereum/Base
function isValidEthAddress(addr) {
  return /^0x[a-fA-F0-9]{40}$/.test(addr);
}

// Solana
function isValidSolanaAddress(addr) {
  try {
    new PublicKey(addr);
    return true;
  } catch { return false; }
}
```

Never process payments or check balances for invalid addresses. Log and reject.

### R-011: RPC Provider Strategy

- Use multiple RPC providers per chain (failover)
- Primary: Alchemy or Infura (Base, Ethereum)
- Primary: Helius or QuickNode (Solana)
- Fallback: Public RPC endpoints (rate-limited, unreliable)
- Monitor RPC health — alert if error rate > 5%
- Never hardcode RPC URLs — use environment variables

### R-012: Liquidity Management

When adding liquidity to DEXes:
- Start with conservative amounts (don't put entire treasury in day 1)
- Use concentrated liquidity on Uniswap V3 (better capital efficiency)
- Set reasonable price ranges based on target market cap
- Monitor for impermanent loss
- Document all liquidity positions in `docs/design-docs/token-strategy.md`

## Definition of Done — Web3 Tasks

A Web3 task is complete when ALL of the following are verified:

For token deployments:

- [ ] Deployed to testnet first, minimum 48 hours of testing (R-002)
- [ ] Chain-specific checklist fully completed (R-003/R-004/R-005)
- [ ] Contract verified on block explorer
- [ ] No secrets in repository (R-001)
- [ ] Security checklist passed (R-009)

For payment integration:

- [ ] Webhook signatures verified on every request (R-006)
- [ ] Full lifecycle tested: checkout → webhook → active → renewal → cancellation
- [ ] Idempotent handlers for duplicate webhook deliveries
- [ ] Database schema matches R-006 specification

For feature gating:

- [ ] Access control logic matches R-008 specification
- [ ] All tiers tested: Free, Bronze, Silver, Gold
- [ ] Token balance check uses latest block (not cached for tier determination)
- [ ] Rate limiting enforced even for highest tier
- [ ] Wallet addresses validated before processing (R-010)

For all Web3 tasks:

- [ ] Handoff produced per `agents/skills/shared/handoff-protocol.md`
- [ ] Deployment logged in Deployment Log section below

## Failure Log

> Deployment failures, payment integration issues, security incidents.

_No failures logged yet._

## Deployment Log

> Track all token and contract deployments.

```
### [DEPLOY-NNN] Chain — Contract Type
**Date**: YYYY-MM-DD
**Chain**: Base / Solana / Ethereum
**Contract Address**: 0x... or ...
**TX Hash**: 0x... or ...
**Verified**: Yes / No
**Explorer Link**: URL
```

_No deployments yet._
