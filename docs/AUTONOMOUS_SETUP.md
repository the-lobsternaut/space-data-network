# Lobsternaut Autonomous Agent Setup Guide

> Everything you need to set up a fully autonomous Lobsternaut agent instance.
> Covers: hardware, accounts, API keys, phone number, identity, and services.

## Overview

To make Lobsternaut a "real boy" — a fully autonomous agent that can post content, deploy tokens, process payments, and manage community without constant human intervention — you need the following infrastructure.

---

## 1. Hardware / Compute

### Your Setup: Mac Mini (Dedicated, Always-On)

You have a Mac Mini, which is ideal — best performance per watt, runs macOS natively (needed for iOS/Xcode builds if you do a mobile app), and more than enough power for the entire Lobsternaut agent stack.

**Setup for always-on operation**:

1. **Prevent sleep**: System Settings -> Energy -> Prevent automatic sleeping when the display is off (enable)
2. **Auto-restart on power failure**: System Settings -> Energy -> Start up automatically after a power failure (enable)
3. **Enable SSH**: System Settings -> General -> Sharing -> Remote Login (enable) — lets you manage remotely
4. **Ethernet over Wi-Fi**: Use wired ethernet if possible for reliability
5. **Dedicated macOS user** (optional but recommended): Create a separate macOS user account `lobsternaut` to isolate the agent's environment, credentials, and processes from your personal account
6. **Homebrew packages**: `brew install cmake emscripten node postgresql@16 gh`
7. **Launch agents**: Use `launchd` plists to auto-start the agent processes on boot (see below)

**Auto-start agent on boot** (create `~/Library/LaunchAgents/com.lobsternaut.agent.plist`):

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>Label</key>
    <string>com.lobsternaut.agent</string>
    <key>ProgramArguments</key>
    <array>
        <string>/bin/bash</string>
        <string>-c</string>
        <string>/path/to/lobsternaut/start.sh</string>
    </array>
    <key>RunAtLoad</key>
    <true/>
    <key>KeepAlive</key>
    <true/>
    <key>StandardOutPath</key>
    <string>/tmp/lobsternaut.log</string>
    <key>StandardErrorPath</key>
    <string>/tmp/lobsternaut.err</string>
</dict>
</plist>
```

**Cost**: $0/mo (hardware you already own, electricity ~$5-10/mo)

### Optional: VPS Backup

If you want redundancy or remote operation when away from the Mac Mini:

| Provider | Plan | Monthly Cost | Notes |
| --- | --- | --- | --- |
| Hetzner | CX22 (2 vCPU, 4GB) | ~$5/mo | Best value, EU-based |
| Railway | Starter | ~$5/mo + usage | Easy deploy for the web backend |

A reasonable pattern: Mac Mini runs the agent, content generation, and builds. A cheap VPS runs the public-facing backend (API, webhooks, web app) with better uptime guarantees.

---

## 2. Phone Number (Required for Most Accounts)

A dedicated phone number is needed for 2FA, account verification, and social media signups.

### Recommended: Prepaid eSIM

| Provider | Plan | Monthly Cost | Notes |
| --- | --- | --- | --- |
| **Mint Mobile** | 5GB plan | $15/mo (prepaid annual = $15/mo) | Best value, T-Mobile network |
| **US Mobile** | 2GB plan | $10/mo | Flexible, multiple networks |
| **Google Fi** | Flexible plan | $20/mo + $10/GB | Good international coverage |
| **Visible** | Basic plan | $25/mo | Unlimited on Verizon |

**Key requirements**:
- Must support SMS (for 2FA verification codes)
- Must be a real carrier number (not VoIP — many services block VoIP)
- eSIM preferred (no physical SIM card to manage)
- Prepaid preferred (no contract, cancel anytime)

**Setup**:
1. Get a cheap Android phone or use a spare phone (iPhone SE works)
2. Activate eSIM with chosen carrier
3. Use this number exclusively for Lobsternaut accounts
4. Set up SMS forwarding or use a 2FA app where possible

### Alternative: Google Voice

- Free, but some services block Google Voice numbers
- Good as a backup, not recommended as primary
- Works for Discord, doesn't work for some crypto exchanges

---

## 3. Email Account

### Dedicated Email (Required)

Create a dedicated email for all Lobsternaut accounts:

| Provider | Recommendation | Notes |
| --- | --- | --- |
| **Gmail** | `lobsternaut.agent@gmail.com` | Free, widely accepted, good API |
| **ProtonMail** | `lobsternaut@proton.me` | Privacy-focused, free tier |
| **Custom domain** | `agent@lobsternaut.ai` | Professional, requires domain purchase |

**Setup**:
1. Create the email account using the dedicated phone number
2. Enable 2FA immediately
3. Generate app passwords for any services that need SMTP access
4. Set up email forwarding to your personal email for monitoring

---

## 4. Apple ID (Required for iOS App Distribution)

If Lobsternaut will have a mobile app (for RevenueCat integration, App Store presence):

### Apple Developer Account

| Item | Cost | Notes |
| --- | --- | --- |
| Apple ID | Free | Create with dedicated email |
| Apple Developer Program | $99/year | Required for App Store distribution |
| Certificates + Provisioning | Included | Managed via Xcode or CLI |

**Setup**:
1. Go to appleid.apple.com, create Apple ID with Lobsternaut email
2. Enroll in Apple Developer Program ($99/year) at developer.apple.com
3. Set up code signing certificates
4. Configure App Store Connect for the Lobsternaut app

### If No iOS App

Skip the Apple Developer Account. You can always add it later. The web app + WASM approach doesn't require it.

---

## 5. Google Developer Account (Required for Android App)

| Item | Cost | Notes |
| --- | --- | --- |
| Google account | Free | Use the dedicated Gmail |
| Google Play Developer | $25 one-time | Required for Play Store |

---

## 6. Social Media Accounts

Create all accounts using the dedicated email and phone number.

| Platform | Username | Account Type | Cost | Notes |
| --- | --- | --- | --- | --- |
| **X/Twitter** | `@LobsternautAI` | Standard | Free | Primary engagement platform |
| **TikTok** | `@LobsternautAI` | Creator | Free | Warm up 2 weeks before posting |
| **LinkedIn** | Lobsternaut company page | Company | Free | Professional content |
| **YouTube** | Lobsternaut | Brand account | Free | Tutorials and explainers |
| **Discord** | Lobsternaut server | Community | Free (Nitro $10/mo optional) | Token-gated channels |
| **Threads** | `@LobsternautAI` | Standard | Free | Casual engagement |
| **Farcaster** | `@lobsternaut` | Standard | Free | Web3-native community |
| **GitHub** | `DigitalArsenal/lobsternaut` | Organization | Free | Code hosting |
| **Reddit** | `u/LobsternautAI` | Standard | Free | Optional, community Q&A |

### TikTok Account Warmup (Critical)

Before the agent posts anything:
1. Use the account as a normal person for 7-14 days
2. Scroll the For You page daily
3. Like sparingly (1 in 10 videos)
4. Follow accounts in the space/science niche
5. Leave genuine comments on space-related content
6. When almost every FYP video is space-related, you're ready

This is the ONE thing that requires a human with a phone. After warmup, the agent takes over via Postiz.

---

## 7. API Keys and Services

### Required (Day 1)

| Service | Purpose | Cost | Signup URL |
| --- | --- | --- | --- |
| **OpenAI API** | Image generation (gpt-image-1.5) | ~$0.50/slideshow | platform.openai.com |
| **Postiz** | TikTok/social posting + analytics | Free tier or $9/mo | postiz.pro |
| **GitHub** | Code hosting, CI/CD | Free | github.com |

### Required (Phase 2 — Token Launch)

| Service | Purpose | Cost | Signup URL |
| --- | --- | --- | --- |
| **Alchemy** | Base + Ethereum RPC | Free tier (300M CU/mo) | alchemy.com |
| **Helius** | Solana RPC | Free tier (50K req/day) | helius.dev |
| **Stripe** | Credit card payments | 2.9% + $0.30 per txn | stripe.com |
| **Coinbase Commerce** | Crypto payments | 1% per txn | commerce.coinbase.com |

### Required (Phase 3 — Community)

| Service | Purpose | Cost | Signup URL |
| --- | --- | --- | --- |
| **Discord Bot** | Server automation | Free | discord.com/developers |
| **Collab.Land** | Token-gated Discord roles | Free tier | collab.land |

### Optional but Recommended

| Service | Purpose | Cost | Notes |
| --- | --- | --- | --- |
| **RevenueCat** | Mobile subscription analytics | Free < $2.5K MRR | Only if building mobile app |
| **Cloudflare** | Domain, CDN, DDoS protection | Free tier | lobsternaut.ai domain |
| **Vercel/Netlify** | Frontend hosting | Free tier | For web demos |
| **Sentry** | Error monitoring | Free tier | Track production errors |
| **Plausible/Umami** | Privacy-friendly analytics | $9/mo or self-host | Website traffic |

---

## 8. Domain and Branding

| Item | Provider | Cost | Notes |
| --- | --- | --- | --- |
| `lobsternaut.ai` domain | Cloudflare / Namecheap | ~$12-50/year | .ai domains are pricier |
| SSL certificate | Cloudflare (free) | Free | Auto-provisioned |
| Logo design | AI-generated or designer | $0-$200 | Optimized for PFP (circle crop) |
| Banner images | AI-generated | $0 | Use OpenAI API or Midjourney |

---

## 9. Crypto Wallets

### Hot Wallets (For Agent Operations)

| Chain | Wallet Type | Purpose |
| --- | --- | --- |
| Base/Ethereum | MetaMask or dedicated EOA | Token deployment, liquidity management |
| Solana | Phantom or CLI wallet | Token deployment, liquidity management |
| Bitcoin | Electrum or native address | Donation receipt |

**Security rules**:
- Hot wallet holds ONLY operational funds (< $1K value)
- Private keys stored in environment variables on the server
- Never committed to git (see web3-integration.md R-001)
- Use a hardware wallet (Ledger/Trezor) for the treasury multisig

### Cold Storage (For Treasury)

| Purpose | Wallet Type | Notes |
| --- | --- | --- |
| Development treasury (20%) | Hardware wallet multisig (2-of-3) | Ledger Nano X recommended |
| Team allocation (10%) | Hardware wallet with timelock | Vesting contract |
| Liquidity reserves | Separate hardware wallet | For adding DEX liquidity |

---

## 10. Database

| Option | Cost | Notes |
| --- | --- | --- |
| **Supabase** (PostgreSQL) | Free tier (500MB) | Easiest setup, includes auth |
| **Railway PostgreSQL** | $5/mo | Co-located with app if using Railway |
| **Neon** (serverless Postgres) | Free tier (512MB) | Auto-scaling, branching |
| **Self-hosted** | Cost of VPS | Full control, more maintenance |

---

## 11. CI/CD

| Service | Purpose | Cost |
| --- | --- | --- |
| **GitHub Actions** | Build, test, deploy | Free (2000 min/mo) |
| **Docker Hub** | Container registry | Free (1 private repo) |

---

## Setup Checklist

### Day 1: Identity and Infrastructure

- [ ] Get dedicated phone number (Mint Mobile eSIM recommended)
- [ ] Create dedicated email (Gmail or ProtonMail)
- [ ] Set up VPS or local machine
- [ ] Create GitHub account/org (if not already done)
- [ ] Push code to GitHub

### Day 2: Social Media Accounts

- [ ] Create X/Twitter account
- [ ] Create TikTok account (begin 2-week warmup)
- [ ] Create LinkedIn company page
- [ ] Create YouTube brand account
- [ ] Create Discord server
- [ ] Create Farcaster account
- [ ] Create Threads account
- [ ] Design logo and branding assets

### Day 3: API Keys and Services

- [ ] Get OpenAI API key
- [ ] Set up Postiz account, connect TikTok (after warmup)
- [ ] Set up Alchemy account (Base + Ethereum RPC)
- [ ] Set up Helius account (Solana RPC)
- [ ] Register domain (lobsternaut.ai)
- [ ] Set up Cloudflare

### Week 1: Token Launch Prep

- [ ] Create hot wallets (MetaMask for Base/ETH, Phantom for Solana)
- [ ] Set up cold storage (hardware wallet for treasury)
- [ ] Deploy Base token via Bankr
- [ ] Add initial liquidity
- [ ] Announce token on X/Twitter
- [ ] Set up Discord with Collab.Land token-gating

### Week 2: Payment Infrastructure

- [ ] Create Stripe account
- [ ] Set up Stripe products/prices for subscription tiers
- [ ] Create Coinbase Commerce account
- [ ] Set up PostgreSQL database
- [ ] Implement webhook endpoints
- [ ] Test payment flow end-to-end (Stripe test mode)

### Week 3+: Content Pipeline Activation

- [ ] TikTok warmup complete — connect to Postiz
- [ ] Begin daily X/Twitter posts (ContentAgent)
- [ ] Begin TikTok slideshow generation
- [ ] Set up YouTube channel, plan first video
- [ ] Begin LinkedIn professional content

### Month 2: Mobile App (Optional)

- [ ] Create Apple Developer Account ($99/year)
- [ ] Create Google Play Developer Account ($25)
- [ ] Set up RevenueCat
- [ ] Build and submit mobile app

---

## Monthly Costs Summary

### Minimum Viable Setup (Mac Mini)

| Item | Monthly Cost |
| --- | --- |
| Mac Mini electricity | ~$5-10 |
| Phone (Mint Mobile prepaid) | $15 |
| OpenAI API (3 slideshows/day) | ~$45 |
| Domain (lobsternaut.ai annualized) | ~$4 |
| **Total** | **~$69-74/mo** |

### Full Production Setup (Mac Mini + VPS Backend)

| Item | Monthly Cost |
| --- | --- |
| Mac Mini electricity | ~$5-10 |
| VPS for backend (Hetzner/Railway) | $5 |
| Phone (Mint Mobile) | $15 |
| OpenAI API (3 slideshows/day) | ~$45 |
| Postiz Pro | $9 |
| Domain | ~$4 |
| Apple Developer (annualized) | ~$8 |
| Stripe fees (on revenue) | 2.9% + $0.30/txn |
| Coinbase Commerce fees | 1%/txn |
| Discord Nitro (optional) | $10 |
| Error monitoring (Sentry) | $0 (free tier) |
| **Total (before revenue)** | **~$101-106/mo** |

### Notes on Costs

- OpenAI image generation is the biggest variable cost
- Use Batch API for 50% discount on scheduled content ($0.25 vs $0.50/slideshow)
- Most blockchain tools have free tiers sufficient for launch
- Stripe/Coinbase fees are only charged on actual revenue
- Scale costs up only when revenue justifies it

---

## Security Checklist

- [ ] All accounts use unique, strong passwords (use a password manager)
- [ ] 2FA enabled on every account (preferably TOTP, not SMS)
- [ ] Dedicated phone number used only for Lobsternaut accounts
- [ ] Private keys stored in environment variables, NEVER in code
- [ ] Hot wallet holds < $1K in operational funds
- [ ] Treasury in hardware wallet multisig
- [ ] `.env` in `.gitignore`
- [ ] Regular key rotation (quarterly minimum)
- [ ] Stripe webhook signatures verified
- [ ] Coinbase Commerce webhook signatures verified
- [ ] Database encrypted at rest
- [ ] VPS firewall configured (SSH key only, no password auth)
