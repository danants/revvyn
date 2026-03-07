# Revvyn — Product Backlog

Last updated: 2026-03-07

---

## 🔴 Phase 1 — Build Now (Pre-Seed / Design Partner Stage)

### Live OFAC SDN Sanctions Feed
Replace simulated sanctions screening in the sandbox with a real, live data check against the OFAC Specially Designated Nationals list.
- Source: https://sanctionslist.ofac.treas.gov/Home/SdnList (free, public, updated daily)
- Every transaction run in the sandbox checks the beneficiary and destination country against the live list
- When it hits, the block is real — not simulated
- **Why now:** When an investor asks "is the sanctions screening real?" the answer becomes yes
- **Effort:** Low — JSON feed, no auth required

### EU Consolidated Sanctions Feed
- Source: https://webgate.ec.europa.eu/fsd/fsf (free, public)
- Complements OFAC for European corridors
- **Effort:** Low

### UN Sanctions Feed
- Source: https://scsanctions.un.org/feed/ (free, public)
- Covers KP, IR, SY and others
- **Effort:** Low

---

## 🟡 Phase 2 — Specialist Agent Layer (Post First Design Partner)

### Regulatory Intelligence Agent
A dedicated agent that monitors live regulatory changes and automatically updates policy rules without manual intervention.
- OFAC/EU/UN sanctions list changes → auto-update blocked country lists
- FinCEN advisories — free RSS feed
- FATF grey/black list changes
- MiCA updates (EU crypto regulation)
- PSD2 amendments
- Feeds into Governance Agent in real time
- **Value:** Revvyn stays current without human oversight — a genuine moat

### Counterparty Risk Agent
Pulls live data on the beneficiary before Governance makes a decision.
- OpenCorporates API — free tier, basic company registry checks
- Companies House (UK) — free API
- SEC EDGAR — free for US entities
- Adverse media screening (open sources)
- PEP (Politically Exposed Persons) flag
- **Value:** Real counterparty verification, not simulated

### FX & Liquidity Agent
Live rate feeds and execution optimisation for cross-border transactions.
- Open Exchange Rates — free tier
- frankfurter.app — completely free, real-time
- Hedging recommendations
- Pre-funded corridor management
- Optimal execution timing
- **Value:** Real FX savings calculations, not estimated

### Fraud Pattern Agent
Trained on transaction history to detect behavioural anomalies.
- Velocity spike detection
- Unusual corridor flagging
- Amount pattern analysis
- Feeds risk signal into Control Agent
- **Value:** Behavioural risk layer on top of static policy rules

### Jurisdictional Compliance Agent
Country-specific rules engine.
- Capital controls by corridor
- Local reporting thresholds (e.g. $10K CTR in US)
- Local licensing requirements
- Knows Brazil ≠ Germany for compliance purposes
- **Value:** Automated jurisdiction-aware compliance — removes manual legal research

---

## 🟢 Phase 3 — Platform Layer (Post Seed Raise)

### Customer Support Vertical
Governance layer for AI agents handling refunds, chargebacks, and compensation payments.
- Target platforms: Zendesk, Freshdesk, Intercom, Jira Service Management
- Agents making financial decisions on support tickets need spend limits, audit trails, escalation paths
- **Why:** Large addressable market, clear pain point, easy to demo

### Extensible Agent Marketplace
Allow enterprise clients to plug in only the specialist agents they need for their vertical.
- Treasury clients → FX Agent + Jurisdictional Agent
- Marketplace clients → Fraud Agent + Counterparty Agent
- Consumer AI clients → Yield Guardian + Self-custody Agent
- **Why:** Turns Revvyn from a point solution into a platform — dramatically increases TAM and defensibility

### ERP Integration Layer
Native connectors to SAP, Oracle, NetSuite for Enterprise Treasury vertical.
- Payment intent originates in ERP, Revvyn governs execution
- Closes the loop from intent to settlement to reconciliation
- **Why:** Enterprise sales motion, high switching costs, strong NRR

---

## Notes

- All Phase 1 items use free public data — no API keys or budget required
- Phase 2 specialist agents are the roadmap story for investor conversations
- Phase 3 is the platform narrative — builds moat and justifies Series A
