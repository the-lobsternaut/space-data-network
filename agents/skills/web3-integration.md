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

**Entitlement lifecycle**:
```
checkout.session.completed → Create purchase/entitlement record
invoice.paid → Renew/confirm recurring entitlement
invoice.payment_failed → Enter 3-day grace period
customer.subscription.deleted → Deactivate recurring entitlement
customer.subscription.updated → Update recurring entitlement policy
```

**Database schema**:
- Store wallet_address + credit_balance + offering_entitlements + stripe_customer_id + status
- Index on wallet_address (primary lookup path)
- Index on stripe_customer_id (webhook lookup path)
- Never store full credit card details (Stripe handles PCI compliance)

### R-007: Coinbase Commerce Integration Rules

- Create charges via Coinbase Commerce API
- Support: BTC, ETH, USDC, USDT, SOL, MATIC
- Webhook: `charge:confirmed` triggers purchase entitlement activation
- Handle `charge:failed` and `charge:pending` states
- Same pricing model as Stripe offerings/credit bundles (USD equivalent)
- Charges expire after 60 minutes — handle `charge:expired`

### R-008: Access Control Logic

The entitlement function checks baseline-free operations first, then purchased entitlements and credit/policy eligibility:

```javascript
async function getEntitlement(walletAddress, operation) {
  const [policy, credits, entitlements] = await Promise.all([
    getAccountPolicy(walletAddress),
    getCreditBalance(walletAddress),
    getPurchasedEntitlements(walletAddress)
  ]);

  if (operation.class === 'baseline-free') {
    return { allowed: true };
  }

  if (operation.class === 'purchased-offering' && entitlements.includes(operation.offeringId)) {
    return { allowed: true };
  }

  if (credits.remaining > 0 || policy.includes(operation.requiredFlag)) {
    return { allowed: true };
  }

  return { allowed: false };
}
```

Rules:
- Baseline-free operations must remain accessible without credits
- Purchased-offering operations must validate receipt/ownership before execution
- Credit-metered operations must verify available balance before execution
- Policy flags may satisfy operation requirements without immediate credit debit
- Token utility can redeem credits and apply fee discounts without creating feature lockouts
- Rate limit: even highest policy class gets hard caps (prevent abuse)

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

For access control:

- [ ] Access control logic matches R-008 specification
- [ ] All paths tested: baseline-free, metered credits, purchased offering, policy overrides
- [ ] Credit checks are accurate and atomic for debited operations
- [ ] Rate limiting enforced even for highest policy class
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
