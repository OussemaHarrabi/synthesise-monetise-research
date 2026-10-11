# Deliverable 8 — Research limitations, unresolved questions and unknowns

**Compiled:** 2026-10-11 · This register is deliberately unflattering. It records
what could **not** be established, what was refused, what conflicts, and what a
future researcher should do next — so that no reader mistakes a well-written
inference for a verified fact.

---

## 1. Hard access failures (recorded, not worked around)

| # | Target | Failure observed | Consequence |
|---|---|---|---|
| A1 | `reddit.com` (native, `.json`, `old.reddit.com`, redlib mirrors, `r.jina.ai`) | JS challenge, then HTTP 403 / redirect-to-login | All Reddit sentiment in this dossier is **second-hand** (via an aggregator) except where a Redlib mirror served the thread. Four threads had their original-post **bodies removed** before capture, so they are unusable. |
| A2 | `trustpilot.com/review/monetise.com` via plain HTTP | HTTP 403 (WAF) | Ratings were instead captured with a headless browser by the customer-evidence workstream — recorded as a methodological substitution, not a clean fetch. |
| A3 | `scamadviser.com` | HTTP 403 | No independent trust/safety score for the domains. |
| A4 | `whop.com/checkout/plan_wPgM4iy64m7Js` | JavaScript-only; no price or terms in the served HTML | The checkout price must be treated as *unknown*; every price claim comes from other surfaces. |
| A5 | `app.synthesise.ai` beyond the anonymous shell | Whop-OAuth gated; **no account was created and no authentication was attempted** | No generated artefact, dashboard, editor, charter or admin screen was observed first-hand. Everything about in-app behaviour is second-hand from screen recordings. |
| A6 | **GitHub code search** | "Sign in to search code on GitHub" | The single most useful reverse-engineering query is **blocked**, not negative. A clone/integration could exist and be invisible to this investigation. |
| A7 | `promptfoo/promptfoo` clone (790 MB) | `curl 56 / early EOF` twice | That repository is scored from metadata only; its eval architecture is UNKNOWN here. |
| A8 | `letta-ai/letta-code` | GitHub API rate limit exhausted (unauthenticated 60 req/h) | The current Letta source was not inspected. |
| A9 | Wayback capture `2026-07-18` of `synthesise.ai` | Redirected to a 2025-10-24 snapshot | The mid-2026 site state could not be read directly. |
| A10 | `synthesise.ai` FAQ answers | The Framer accordion answers are absent from the HTML, the markdown conversion **and** the Framer search index | The five FAQ questions are known; **their answers are UNKNOWN**. |
| A11 | `theainavigator.com` Synthesise article | JS shell (Strikingly); body not extractable | One secondary review unavailable. |
| A12 | `freesummitaccess.online/synthesize-ai/` | HTTP 404 | A cited third-party review is now dead. |
| A13 | `source=companies_house` for the principal operator | Companies House is **UK-only**; the operator is a **UAE free-zone** company | Ownership, shareholders, UBO and accounts of IAG Services FZCO are **UNKNOWN**; only an aggregator's address/contact record exists. |
| A14 | YouTube transcripts for two reviews | Not retrievable in the originating workstream (later obtained by another: `.vtt` files exist for `_bfnnPIwMPE`, `SFTNtOtKBp8`, `TDZiyVNe8CI` in `raw/yt/`) | Partially resolved; treat any unquoted video claim as unavailable. |

---

## 2. Questions that remain open

### 2.1 Synthesise AI internals (the biggest block)

| Question | Status | Why it stays open |
|---|---|---|
| Which LLM provider(s), models and versions? | **UNKNOWN** | Zero provider/model tokens in 41 client chunks; server-side only. |
| What do the prompts say? | **UNKNOWN** | No leak, no injection report, no public input/output example, no code. |
| How many agents, and are they agents at all? | **UNKNOWN (0 verified)** | The route table shows pipeline stages; no agent is observable. |
| Orchestration topology (sequential / graph / event-driven)? | **UNKNOWN** | Only nesting and per-stage screens are observable. |
| What is `vtm`? | **INFERENCE only** | Plausibly "value/visual transformation map"; the expansion is never printed. |
| What does `/admin/models` / `/admin/ai` do? | **UNKNOWN** | Routes exist; the namespaces 404; the pages are auth-gated. |
| Database, ORM, queue, vector store? | **UNKNOWN** | No placeholder strings, connection keys or client configs recoverable. |
| Is there any fact-checking or grounding? | **Not evidenced anywhere** | A negative finding from an exhaustive client-side surface study, not proof of absence. |
| Is UVZ computed with ML as claimed? | **UNKNOWN; no ML evidence** | "Machine learning", "cosine similarity", "vectorized corpus" appear only in marketing and in the rejected GitBook. |
| Is the `$97/month` Whop storefront authorised? | **UNKNOWN** | Different legal footprint (Montréal), 5 followers, $0 earned; could be a reseller, an impersonation, or a legitimate second channel. |
| Was the product built in-house or acquired/white-labelled? | **UNKNOWN** | One practitioner asserts on video that it was purchased; that is a **user inference**, not evidence. |

### 2.2 Ecosystem

| Question | Status |
|---|---|
| Who currently operates Flozy after `AGENCIFLOW LTD` dissolved (2026-10-06)? | **UNKNOWN** (asset sale / re-domicile / abandonment all consistent with the evidence) |
| What is the relationship of the Brazilian footer entity `RIBEIRO NEGOCIOS DIGITAIS E CONSULTORIA LTDA` (CNPJ 49.587.630/0001-01)? | **UNKNOWN** |
| UpLevel Consulting and Monetise Pro prices | **Not published**; only third-party estimates. |
| Is the "Microsoft Founders Hub" relationship real? | **UNOFFICIAL** — claimed on the landing page and in the GitBook; **no Microsoft page confirming it was located**. The programme itself is a startup-credits scheme, not an endorsement. |
| Does any non-UK "Business Together" entity exist? | **UNKNOWN** — the negative is verified only for the UK register. |
| Who owns the `$SYNAI` token, and is the GitBook official? | **UNKNOWN, likely unauthorised/AI-generated.** Authorship unverifiable; contradicted by the deployment; not linked from the official site. |

### 2.3 Customer evidence

| Question | Status |
|---|---|
| What do customers say at a sample size that permits generalisation? | **Not achievable here.** Trustpilot Monetise n=11 (73 % one-star); Whop n=4,353 (4.8★, solicited on a platform the founder co-owns). Both ends are contaminated in opposite directions. |
| Are the positive Whop reviews incentivised? | **UNKNOWN**, though solicited post-purchase review flows are standard on Whop. |
| How many refunds are actually refused? | **UNKNOWN** — individual reports exist (9 independent reporters), no aggregate is published. |
| Does Synthesise AI have any independent review surface at all? | **No** — no Trustpilot profile, no G2/Capterra, no Product Hunt listing, no app-store app, no status page or changelog was found. This absence is itself the most robust customer-evidence finding. |
| Does the generated output actually sell? | **UNKNOWN** — no tool-attributed sales evidence was found anywhere; the "tool-attributed" figures are aggregate Whop/member numbers. |

### 2.4 Open-source audit

| Question | Status |
|---|---|
| `promptfoo` internals | UNKNOWN (clone failed) |
| `letta-code` licence and architecture | UNKNOWN (API rate limit) |
| Which `microsoft/agent-framework` packages are GA vs preview | UNKNOWN (status file seen, not read) |
| Do the recommended libraries compose into a passing export pipeline? | **Untested** — nothing was executed; no EPUB was opened, no PDF rendered, no test suite run. |
| Release cadence of the scored repos | Shallow clones have no history; cadence rests on `pushed_at` and in-repo changelogs. |
| Security posture (CVEs, dependency risk) | **Not assessed** — no `pip-audit`/`osv-scanner` run. |
| `pandoc` licence | **badge-only**, from the GitHub API, not the `COPYING` file — re-verify before depending on it. |

---

## 3. Contradiction register (with resolution status)

| # | Contradiction | Status |
|---|---|---|
| C1 | Synthesise AI: "$2,999 one-time" vs "monthly subscription basis" (same domain, same day) | **EXPLAINED (medium-high):** the ToS is inherited template text — corroborated by the sibling Educate.io/Digital Launchpad terms sharing §11 verbatim and by the unfilled legal placeholder in the Privacy Policy. Operative reading: one-time payment, 12-month access, 7-day refund. |
| C2 | "$1,499 (3 credits)" vs "$2,999 (unlimited)" vs "$97/month" vs "$1,995" | **RESOLVED as different objects:** a 2025 lower tier; the current price; a separate Montréal Whop storefront of unverified provenance; the Monetise bundle price. |
| C3 | "$97/month Synthesise AI" Whop listing | **UNRESOLVED** — provenance unknown; possible impersonation. |
| C4 | "Unlimited Credits" vs "12 months, subject to fair use" | **RESOLVED:** unlimited *within* the 12-month window, with a fair-use cap and suspension without refund for abuse. |
| C5 | Ghostwriter OS: "$2,999/yr" vs its Terms' "monthly subscription basis" | **UNRESOLVED** — same template defect class as C1, not independently verified. |
| C6 | Flozy user count: 42,000+ / 30,000+ / "over 50,000 clients" | **UNRESOLVED** — three vendor surfaces, three numbers; no methodology. |
| C7 | Marketing stack value: $15,293 → $19,458 → $8,659 "fair price" → $1,995 | **RESOLVED as a selling technique** (descending anchor), evidenced by receipt-holding reviewers. |
| C8 | Governing law: UAE/DIAC (Synthesise, Educate, Monetise) vs **UK/London arbitration** (Ghostwriter OS) | **UNRESOLVED** — internal inconsistency across sibling products. |
| C9 | GitBook stack (Rust/WASM/K8s/Postgres/Stripe, 4 agents, $SYNAI token) vs the live Next.js/Vercel/Whop deployment | **RESOLVED — the GitBook is rejected.** Contradicted by first-hand evidence; its code samples are stubs. |
| C10 | "150+ sources with a reference list" claim vs no citation UI ever shown | **UNRESOLVED** — the claim was made live and never demonstrated. |
| C11 | "$3bn Whop sales data" claim | **UNRESOLVED and unverifiable by every source reviewed, including the affiliate repeating it.** |
| C12 | "5-stage transformation map" (2025 copy) vs 4 stages observed in the 2025–26 builds | **RESOLVED as version drift.** |
| C13 | GADZHI eyewear vs a national-paper reference to "Hills" | **UNRESOLVED (low confidence)** — likely a press error or a rename. |
| C14 | Factory README claims `ZERO_ACTIVITY`/`LOW_ACTIVITY`/`MEASURED_ACTIVITY` metrics; they appear **nowhere** in `ipf/` or `tests/` | **RESOLVED — the README is wrong** (`NO_DATA` and `UNMEASURED` are the real vocabulary). |
| C15 | `API_CONTRACT.md`/`openapi.yaml` say "No HTTP server exists in the repository today" while a 44-route server runs; a documented `PATCH /campaigns/{id}` 404s | **RESOLVED — the docs are stale.** |

---

## 4. Unverified technical assumptions in this dossier

Stated explicitly so they are not read as findings:

1. `not evidenced` ≠ `does not exist`. Every absence claim (no fact-checking, no
   agents, no vector store, no Stripe) is bounded by what an anonymous client-side
   inspection can see.
2. **`files.generateSasUrl` ⇒ Azure Blob** is a naming inference. The Microsoft
   Founders Hub claim is the only corroboration, and it is an OFFICIAL CLAIM.
3. **`vtm` ⇒ transformation map** is an inference from route position and from the
   user reports describing a transformation-map step.
4. **Next.js ≥ 16.3** rests on the `/_next/static/immutable/` path form plus the
   RSC `Vary` keys; the version itself is a strong inference, not a printed version.
5. **The `$97/month` storefront is unaffiliated** is a hypothesis, not a finding.
6. **"Agents"** — the design in Deliverable 6 is our engineering proposal and is
   **not** a reconstruction of anything Synthesise AI uses. No prompt in this
   dossier is the product's.
7. **The repo's line numbers may drift**: a concurrent editor changed
   `ipf/api/server.py` and `ipf/database/migrations.py` while the audits were
   running, and the working tree was already dirty before this work began.
8. **Test counts differ by runner**: 1,436 via `unittest` discovery vs 1,421
   collected by `pytest` (which also records 226 passing subtests). Neither number
   implies the suite is green — **3–4 tests fail and the failing set is
   non-deterministic across runs**.

---

## 5. Searches and retrievals that returned nothing (do not repeat these blindly)

* GitHub repository search `synthesise ai` → 147 results, all unrelated.
* GitHub repository search `iman gadzhi` → 13 results, 1 relevant
  (`Master-Urus/Shadow-Operator`, a concept-level reimplementation).
* GitHub issue search `"synthesise ai" OR "monetise ai"` → 155 results, all generic
  uses of the words.
* npm registry `synthesise` → unrelated packages. PyPI → unrelated synthetic-data
  packages. Hugging Face models API `?search=synthesise` → `[]`.
* 25 distinct web queries for prompts, leaks, reverse engineering, API docs and
  integrations → nothing product-specific.
* Companies House: no Gadzhi-linked "Business Together" company.
* No Trustpilot/G2/Capterra/Product Hunt/app-store presence for Synthesise AI.
* Factory repo: `grep` for `requests.`/`urlopen` in `ipf/products`+`ipf/authoring`
  returns only the LLM call — i.e. **no URL resolution or fact-check code exists**
  (this is a *negative finding*, and it is load-bearing for the gap analysis).

---

## 6. Next legitimate research steps (ranked by value per unit of effort)

1. **Authenticated observation, if access is legitimate.** The single highest-value
   gap is in-app behaviour: a Monetise-licensed session would let one observe the
   real UVZ scoring UI, whether citations are shown, the actual export formats, and
   whether `/admin/models` exposes a model picker. This requires *authorised* access
   — nothing here should be obtained by circumventing a paywall, and this
   investigation deliberately did not.
2. **Re-run the client-bundle snapshot weekly** and diff the route table and the
   tRPC oracle results. It is cheap, it is first-hand, and it is the only reliable
   way to track this product's feature surface (chunk hashes rotate per deployment).
3. **Authenticated GitHub code search** (`synthesise.ai`, `parseGWOSAudit`,
   `unique-value-zone`) — the one blocked query class that could reveal clones or
   integrations.
4. **Watch the `$SYNAI`/GitBook question**: if a token sale does occur against this
   brand, it is material to any customer of the product, and it currently has no
   verifiable owner.
5. **Factory side, and it costs nothing:** run the golden-set harness (R-05) against
   the current baseline *before* any change. Until that exists, no claim of
   improvement in this dossier can be tested.
6. **Ask the platform, not the docs, for the factory's real state**: the API
   contradicts `openapi.yaml`; a fresh `GET /health` plus a route dump is a better
   source of truth than any committed document.

---

## 7. Programme-level method limitations

1. **Seven parallel workstreams, one synthesiser.** Each workstream is documented
   with its own method limits; the synthesiser (this dossier) may have flattened a
   nuance. Where a raw file and this dossier disagree, **the raw file and its source
   ids win.**
2. **Two cross-workstream corrections were issued and are recorded** rather than
   silently fixed: the auth label (NextAuth → **Better Auth**) and the
   `store.buyer.*` procedure claim (withdrawn — `-32004 NOT_FOUND`).
3. **Time-boxing.** Every workstream stopped on diminishing returns, not on
   completeness; §2 lists what that left open.
4. **Volatility.** Prices, refund windows, bundle contents and product names in this
   ecosystem change on a roughly quarterly launch cycle. Every commercial claim in
   this dossier is dated; none should be quoted without its date.
5. **Adversarial content.** Several reviewed surfaces (affiliate review sites on both
   sides of the argument, a competing product's own marketing, and a GitBook that
   reads as machine-generated) have incentives to misstate. Where a claim's only
   support was such a surface, it is classified OFFICIAL CLAIM or USER-REPORTED and
   never VERIFIED.
6. **Two factory areas are covered structurally rather than line-by-line.**
   `runs/rebuild_{fitness,niche,screen}_book.py`, `runs/run_teacher_pipeline.py` and
   `runs/build_study_product.py` were mapped by stage/artefact greps, not read line
   by line; and `web/` (the Creator Studio UI) was inspected only at the API-client
   level. Claims about those files are therefore coarser than the rest of the audit.
7. **The factory's newest capability is the least audited.** Commit `03bf3b5`
   ("Item 1 (MODEL authoring) + item 2 (real opportunity research)") landed mid-audit;
   its 1,890 inserted lines were audited as untracked working-tree content. That work
   is now committed, so the audited content *is* the committed content — but line
   numbers drifted with the commit, and only the P0 defects were re-verified
   afterwards rather than the whole audit being re-run end to end.
