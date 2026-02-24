# Lobsternaut

> **Astrodynamics AI Agent with Multi-Chain Token Economy**

Lobsternaut is a comprehensive astrodynamics AI platform combining professional-grade C++ orbital mechanics software (OrbPro) with Web3 tokenomics and mainstream payment accessibility.

## 🚀 Key Features

- **OrbPro C++ Compute Engine**: Production-grade astrodynamics compiled to WebAssembly
- **Lobsternaut Software Client**: NanoClaw-compatible runtime with embedded SDN node
- **Multi-Chain Tokens**: Deployed on Base, Solana, Ethereum
- **Tiered Access**: Six tiers (five per-seat + AI Enabled usage-based)
- **Flagship Feature**: Conjunction assessment and collision avoidance
- **Bring Your Own Inference**: Use local models or paid inference providers
- **Embedded MCP**: SpaceAware AI subscription unlocks MCP-powered automation workflows
- **Source Transparency**: Published links to upstream open-source astrodynamics code used to build OrbPro

## 📚 Documentation

- [Strategic Plan](./STRATEGIC_PLAN.md) - Complete implementation strategy
- [OrbPro Upstream Sources](./docs/references/orbpro-upstream-sources.md) - Linked upstream open-source code used in OrbPro
- [Creative Prompt Pack](./docs/creative-prompt-pack.md) - Apple-level design system and marketing prompt templates

## 🔗 Links

- **Website**: Coming soon
- **Twitter/X**: [@LobsternautAI](https://twitter.com/LobsternautAI)
- **Discord**: Coming soon
- **GitHub**: [github.com/DigitalArsenal/lobsternaut](https://github.com/DigitalArsenal/lobsternaut)

## 💰 Token Information

- **Ticker**: $CLAW
- **Chains**: Base, Solana, Ethereum
- **Supply**: 1,000,000,000 total

## 🛠️ Contributor Workflow (Optional)

- **Plan rigor**: Follow non-trivial-task planning rules in `agents/skills/planning.md`.
- **Completion safety**: For stronger session-level completion checks, you can install the Taskmaster stop-hook:
  - `git clone https://github.com/blader/taskmaster.git`
  - `cd taskmaster`
  - `bash install.sh`

### Taskmaster-like manual mode (no hooks)

If you prefer not to install hooks, keep the same behavior with a manual completion gate:

- Report each objective as `done / partial / blocked`.
- Include the completion-gate checklist from `agents/skills/planning.md` (R-012) in your final response.
- If any item is `partial` or `blocked`, continue execution before marking complete.

## 🛠️ Tech Stack

- C++17/20 (OrbPro compute engine)
- WebAssembly (Emscripten)
- JavaScript/TypeScript (API wrapper)
- Solidity (Smart contracts)
- Node.js/Express (Backend)
- PostgreSQL (Database)

## 📈 Access Tiers

| Tier | Price | Model | Key Additions |
|------|-------|-------|---------------|
| Free | $0 | Per seat | Conjunction assessment (CDMs), SGP4/SGP4-XP, high-def propagation, 3D globe |
| Explorer | $10/mo | Per seat | Link sharing, 10 saved scenarios, exports, alerts, embed widget, bookmarks |
| Analyst | $20/mo | Per seat | 100 saved scenarios, Basilisk simulator, Lambert/Hohmann planning, API access (25K/day) |
| Operator | $30/mo | Per seat | Monte Carlo, launch/reentry and missile simulation, 500 scenarios, CA workflow |
| Mission | $40/mo | Per seat | RPO/proximity ops, combat sim, EW, multi-domain modeling, unlimited scenarios |
| AI Enabled | $70 baseline (usage-based) | Usage-based | SpaceAware AI subscription, embedded MCP workflows, AI copilots, autonomous workflow automation, priority AI compute, all Mission features |

## 🤝 Contributing

Contributions welcome! Please read our contributing guidelines first.

## 📄 License

MIT License - See LICENSE file for details

---

*Making orbital mechanics accessible to everyone through transparent engineering references and Web3 incentives.*
