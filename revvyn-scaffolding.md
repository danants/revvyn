# Revvyn — Scaffolding Map
**What is built. What is mocked. What needs doing before a live pilot.**

---

## Summary

The core intelligence is real. The integrations are not.

Every agent runs real logic. The policy engine enforces real rules. Rail selection uses real cost and corridor data. The audit chain is complete. What is missing is live connectivity — the rails return mock transaction IDs, sanctions check a country list instead of OFAC, KYC is a pass-through, and LLM reasoning is configured but not wired into the agent decisions.

---

## 1. The Five Agents

| Agent | Status | What's real | What's mock |
|---|---|---|---|
| **Orchestrator** | ✅ Real | Validates intent source, vertical, currency, amount. Classifies priority (standard / medium / high / critical). | Nothing — fully deterministic, no external dependency needed. |
| **Governance** | ✅ Real | Policy evaluation: spend limits, daily volume, sanctioned country list, yield-only enforcement, principal protection, HITL thresholds. Outputs compliance score and regulatory tags. | Sanctions is country-list only (see Section 3). KYC is a pass-through flag. No live screening API. |
| **Control** | ✅ Real | Risk scoring across 6 weighted factors (amount, cross-border, corridor, stablecoin size, urgency). Calls rail registry for optimal rail selection. | Risk model is rule-based, not ML. New-beneficiary flag exists in RISK_FACTORS but not evaluated (no beneficiary history store). |
| **Execution** | ✅ Real | Dispatches to selected rail. Calculates fees. Records transaction ID, settled_at, fee breakdown. Returns settlement confirmation. | All 9 rails are sandbox (see Section 2). Execution logic is correct; the rails it calls are not live. |
| **Audit** | ✅ Real | Reconstructs full decision chain. Assigns regulatory tags (FATF, PSD2, MiCA, SEPA, GENIUS_ACT). Classifies compliance status and risk level. Creates 7-year retention record. | Nothing — fully internal, no external dependency. |

---

## 2. The Nine Rails

All nine are **sandbox implementations**. They follow real API specs (correct fee structures, speed characteristics, corridor constraints, regulatory tags) but return `secrets.token_hex()` transaction IDs. No live API calls are made.

| Rail | Provider | Swap-out needed |
|---|---|---|
| `visa` | Visa | Visa Direct / CyberSource API |
| `mastercard` | Mastercard | Mastercard Send / Connect+ API |
| `swift` | SWIFT gpi | SWIFT gpi Connector or bank middleware |
| `sepa` | SEPA Instant | Bank / PSP with SEPA Instant access |
| `wooshpay` | WooshPay | WooshPay REST API |
| `stablecoin` | Circle / USDC | Circle Programmable Wallets or CCTP |
| `rtp` | RTP / FedNow | TCH RTP or FedNow API (US only) |
| `defi` | Aave / Compound | On-chain via ethers.js / web3.py |
| `self_custody_card` | Wirex / Gnosis | Wirex Business API |

Each rail class is in `app/rails/sandbox.py`. The base class in `app/rails/base.py` defines the interface. Swapping to live = implement `execute()` and `check_status()` against the real API, keep everything else unchanged.

---

## 3. Compliance Checks

| Check | Status | Detail |
|---|---|---|
| **Sanctioned countries** | ⚠️ Partial | Checks `to_country` and `from_country` against the policy YAML list (`KP, IR, SY, CU, RU`). No live OFAC, EU Consolidated, or UN API call. No entity name screening. |
| **Entity fuzzy matching** | ❌ Not built | Documented in the bible. Not in the codebase. Beneficiary name is stored but never screened against a watchlist. |
| **KYC** | ⚠️ Pass-through | Policy flag `kyc_required` exists. Engine logs "pass-through in sandbox". No Jumio, SumSub, or equivalent wired. |
| **AML** | ⚠️ Partial | `aml_screening_required` policy flag exists. No transaction monitoring or behavioural pattern detection. |
| **Travel Rule** | ⚠️ Flag only | `travel_rule_required` in policy YAML. Not enforced in code. |
| **Daily volume limits** | ✅ Real | Queries PostgreSQL for cumulative tenant volume. Fully implemented. |
| **Single payment limits** | ✅ Real | Enforced against `max_single_payment` from policy. |
| **Yield / principal protection** | ✅ Real | `yield_only_spending` and `principal_protection` flags enforced. Yield budget check implemented. |
| **HITL thresholds** | ✅ Real | `approval_threshold` and `dual_approval_threshold` trigger escalation correctly. |

---

## 4. AI / LLM Reasoning

The agents use **deterministic rule logic**. They do not call Claude or OpenAI.

`anthropic_api_key` and `openai_api_key` are in `config.py`. Neither is wired into any agent. The agents reason via Python conditionals and weighted scoring, not language model inference.

This is fine for the sandbox and arguably preferable for auditability. LLM calls would be the right addition for: edge case policy interpretation, natural language reason codes, and anomaly detection on unusual transaction patterns.

---

## 5. Live Data Feeds

| Feed | Status | Detail |
|---|---|---|
| **FX rates** | ❌ Not wired | The bible and README mention ECB via `frankfurter.app`. Not in the codebase. FX is referenced in rail capabilities (`supports_fx: true`) but no live rate is fetched at execution time. |
| **OFAC / sanctions lists** | ❌ Not wired | See Section 3. |
| **Beneficiary history** | ❌ Not built | `new_beneficiary` is a risk factor in Control but there is no lookup store to evaluate it against. Always scores zero. |

---

## 6. Infrastructure

| Component | Status | Detail |
|---|---|---|
| **FastAPI app** | ✅ Real | Routes wired: payments, rails, tenants, kill switch, escalation, audit. |
| **PostgreSQL models** | ✅ Real | Payment, Policy, AuditSummary — async SQLAlchemy 2.0. |
| **Auth** | ✅ Real | API key (tenant) + JWT (dashboard). `app/core/auth.py`. |
| **Docker** | ✅ Real | `docker-compose.yml` + `Dockerfile`. Starts app + Postgres. |
| **Default policies** | ✅ Real | `policies/defaults.yaml` — 5 verticals with real thresholds. Loaded on tenant creation. |
| **Dashboard / UI** | ❌ Not built | JWT auth is ready. No frontend exists. HITL escalation decisions require the API directly. |
| **Kill switch UI** | ❌ Not built | `POST /v1/kill-switch` endpoint works. No dashboard toggle. |
| **Tests** | ⚠️ Skeleton | `tests/` directory exists, `__init__.py` only. No test coverage. |

---

## 7. What's Needed Before a Pilot

Minimum viable for a paying pilot customer (one vertical, one rail):

1. **One live rail** — SWIFT or SEPA is the right start for Enterprise Treasury. Replace `SwiftRail.execute()` with a real gpi call via a banking middleware partner.
2. **Live sanctions screening** — Integrate Chainalysis, ComplyAdvantage, or Refinitiv World-Check for entity + country screening. One API call, replaces the country-list check.
3. **FX rate feed** — Wire `frankfurter.app` into the execution step. 15 minutes of work.
4. **Dashboard (minimal)** — HITL escalation decisions need a UI. A simple read-only audit view + approve/reject button is enough for pilot.
5. **One real test suite** — At minimum: happy path treasury payment, sanctioned country rejection, spend limit breach, kill switch halt.

Everything else — the other 8 rails, KYC integrations, LLM reasoning, AML monitoring — is post-pilot.

---

*Generated from codebase inspection — `app/`, `policies/`, `README.md` — March 2026.*
