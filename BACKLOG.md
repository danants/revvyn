# Revvyn — Backlog

Last updated: March 14, 2026

---

## IMMEDIATE (Next Session)

- [ ] Expose rail scoring breakdown in control agent audit output (show all candidate scores, not just winner)
- [ ] Add rail scoring comparison to dashboard (SEPA: 72 / Bridge: 89 / x402: 45 → Selected: Bridge)
- [ ] Surface ERC-8004 / Verifiable Intent status in KYA dashboard panel
- [ ] Add "11 rails · BaseRail extensible" message to rails tab
- [ ] Update seed_dev.sql with registered_agents insert
- [ ] Add CORS `*` for dev environment so dashboard works without Python server workaround
- [ ] Add static file route to FastAPI so dashboard serves at `/dashboard`
- [ ] Record demo — 90 seconds, dashboard + Stripe split screen, 3 scenarios (approved, rejected, kill switch)
- [ ] Write voiceover script for the demo

## THIS WEEK

- [ ] Build pitch deck in Gamma with insert content (4 slides ready)
- [ ] Update one-pager with KYA, 3 rails, competitive landscape
- [ ] Update bible with KYA agent, x402 rail, Bridge rail, Circle hackathon evidence
- [ ] LinkedIn post with demo video (same playbook as CloseLoop)
- [ ] DM Simon Taylor with demo — reference March 3 conversation

## POST-DEMO

- [ ] Human-in-the-loop approval flow for escalated payments (resolve via API endpoint already exists)
- [ ] Agent transaction history auto-updating (daily_spent, monthly_spent, total_transactions increment on settlement)
- [ ] Wire real AgentProof API as trust signal input to KYA agent
- [ ] Refund pipeline — reverse governance (payment flows backwards through same 6 agents with same enforcement)
- [ ] Serve dashboard on Render alongside API
- [ ] Landing page for revvyn.com

## SERIES A — Agent Accounts

KYA already contains the account primitives: identity, trust score, exposure ceiling, currency permissions, transaction history. Adding balance and credit transforms Revvyn from governance middleware into the financial kernel for the AI economy.

- [ ] Agent balance field (hold funds)
- [ ] Agent credit system (exposure ceiling → credit line)
- [ ] Agent-to-agent settlement
- [ ] Agent account dashboard (balance, credit, exposure, history)
- [ ] Regulatory assessment for custody/credit implications

Architecture:

```
AI Agents
    │
Agent Accounts
(identity · balance · exposure · trust)
    │
Revvyn Financial Kernel
(KYA · Governance · Control · Execution · Audit)
    │
Payment Rails
(SEPA · Stablecoin · x402 · Cards · DeFi)
```

## SERIES A — x402 Middleware (Option 2)

Revvyn becomes a transparent proxy in the HTTP flow between agents and x402 endpoints. Agent doesn't know Revvyn is there — makes an x402 request, Revvyn intercepts, governs, forwards or blocks.

Requires:
- [ ] HTTP proxy architecture (intercept 402 responses)
- [ ] x402 SDK integration (parse payment instructions)
- [ ] Wallet management (hold and release USDC on behalf of agents)
- [ ] Real-time settlement monitoring on Base/Solana
- [ ] Production infrastructure

## SERIES A — Regulatory & Compliance Infrastructure

- [ ] Regulatory reporting automation — one-click compliance reports (PSD2, FATF, FinCEN, MiCA)
- [ ] Jurisdiction policy templates — pre-built configs (US/FinCEN, EU/MiCA, UK/FCA, SG/MAS, UAE/VARA) with auto-updates when regulations change
- [ ] SOC2 Type 1 certification timeline

## PARKED

- [ ] Stripe Financial Accounts / Bridge live integration (requires $10-20K/month — revisit post-funding)
- [ ] Circle API live USDC integration (requires onboarding)

## GITHUB HOUSEKEEPING

- [ ] Fix git committer name/email (`git config --global --edit`)
- [ ] Add `.DS_Store` to `.gitignore`
- [ ] Tag current state as `v0.2.0` — KYA + multi-rail

---

## GUARDRAILS

Before building anything, it must pass:

1. Does this strengthen agent payment governance?
2. Does this increase the trust/compliance story?
3. Does this move toward the financial kernel vision?
4. Would removing this weaken the demo?

**Explicitly rejected:**
- Analytics dashboards without governance purpose
- Agent productivity scoring
- AI predictions / speculative risk models
- Workflow builder features
- Any feature that observes without enforcing
