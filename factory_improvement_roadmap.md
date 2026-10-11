# Deliverable 7 — Prioritised improvement roadmap and validation plan

**Compiled:** 2026-10-11 · **Target:** `C:/Users/DELL/ipf-factory`
**Basis:** `factory_gap_analysis.md` + `proposed_agent_and_skill_architecture.md` +
the audits in `raw/`. Every "Evidence" cell points at a file:line finding, not an
opinion.

**Priority key** · **P0** required before the factory can produce reliably useful,
differentiated products · **P1** high value, do after the foundation is proven ·
**P2** valuable, wait until the foundation is validated.

**Complexity key** · S ≤ a day · M 2–5 days · L 1–3 weeks (for one competent
engineer with the existing test suite).

---

## 1. Decision summary

| # | Item | Pri | Complexity | Blocks / blocked by |
|---|---|---|---|---|
| R-01 | Transformation-derived outline + `OutlineGate` | **P0** | M | blocks R-04, R-06 |
| R-02 | Claim-level citation binding | **P0** | M | independent |
| R-03 | Wire the semantic critic into the product path | **P0** | S–M | benefits from R-02 |
| R-04 | Un-dead-end the control plane (mechanism / competition / feasibility) | **P0** | M | blocks R-01 end-to-end testing |
| R-05 | Golden-set evaluation harness | **P0** | M | needed to judge everything else |
| R-06 | Close the two fabrication paths + enforce the demand gate | **P0** | S | independent, high severity |
| R-07 | API auth hardening (token required, no wildcard CORS, no browser path) | **P0** (if ever exposed) | S | independent |
| R-08 | Fix the research loader preference + evidence relevance assertion | **P0** | S | independent |
| R-09 | Deterministic test suite; remove source-text assertions | **P1** | M | supports all |
| R-10 | Niche descriptor as data | **P1** | S–M | enables new niches |
| R-11 | Stage-level resumability + idempotent stages | **P1** | M | — |
| R-12 | Tool/worksheet model | **P1** | S–M | benefits from R-01 |
| R-13 | Offer / packaging / sales-asset stage | **P1** | M | needs R-01 |
| R-14 | Exporter: PDF + EPUB3 (in-house) + gates | **P1** | M–L | needs R-02 for anchor fidelity |
| R-15 | Cost/token accounting + model tiering | **P1** | S–M | needed for R-05 economics |
| R-16 | SQLite thread-safety + migration error propagation | **P1** | S–M | — |
| R-17 | Doc/code truth: README, OpenAPI, metric vocabulary | **P1** | S | — |
| R-18 | Dead-code removal (`CreatorScorer`, `EvidenceStore`, `reader_persona` preflight, unreachable `LATE`, `DpfBridge` decision) | **P1** | S | — |
| R-19 | Publish/delivery integration (Whop sandbox) | **P2** | M | needs R-13 |
| R-20 | Image/asset generation adapter | **P2** | S–M | — |
| R-21 | Institutional export gates (epubcheck/ace) as CI checks | **P2** | S | needs R-14 |
| R-22 | SQLite FTS5 search over evidence (before any vector DB) | **P2** | S | — |

---

## 2. P0 items in detail

### R-01 — Transformation-derived outline + `OutlineGate`

* **Problem.** `ipf/products/factory.py::compose_chapters` contains a literal
  8-item chapter `plan`; every product the API path emits has the same titles, and
  worksheet/callout/source blocks are injected by `if i == 0 / 3 / 4 / 5 / 6 / 7`.
  Nothing in the pipeline models a before/after state or a milestone.
* **Evidence.** pipeline audit findings 5, 6; `factory.py:268-292`, `:311-335`.
* **Implementation.** Add `ipf/authoring/transformation.py` (`ReaderProfile`,
  `TransformationSpec`, pure dataclasses) and
  `ipf/authoring/outline.py` (`build_outline(brief, transform, evidence) -> Outline`,
  `validate_outline(outline, transform, evidence, budget) -> (ok, failures)`).
  Replace `compose_chapters`'s plan with `outline.chapters`. Call the LLM once for
  the outline (prompt in Deliverable 6 §3 R4) and never for the plan again.
* **Reuse.** `instructor` (MIT) for the JSON schema + reask loop; the repo's own
  `ValidationStatus` and `FactoryError`/`PrerequisiteError` codes for failure
  reporting. No framework.
* **Expected benefit.** The single largest quality lever: structure that serves the
  reader's outcome instead of a fixed template.
* **Dependencies.** R-04 (a real opportunity must be able to reach production);
  R-05 to prove it worked.
* **Risks.** An LLM-produced outline may drift to generic structures → mitigated by
  the deterministic `OutlineGate` (milestone coverage, evidence-slot existence,
  budget bounds, tool count, duplicate-title check) and by bound-once/regenerate-once.
* **Testing.** Unit: gate rejects missing milestone coverage, unknown evidence ids,
  over/under-budget plans, duplicate titles. Integration: two different briefs
  produce different outlines with every chapter mapped to a milestone.
* **Acceptance criteria.** (a) For 3 distinct briefs, no two share a chapter-title
  list; (b) every chapter declares `covers_milestone` ∈ the spec; (c) a deliberately
  broken outline is refused with a machine-readable failure list; (d) no chapter is
  authored when the gate fails.
* **Metric.** *Milestone coverage* = % of chapters whose objective cites a milestone
  (target 100 %); *structural distinctness* = mean pairwise Jaccard of chapter
  titles across 10 briefs (target < 0.2, down from 1.0 today).

### R-02 — Claim-level citation binding

* **Problem.** `inject_citations` attaches `[n]` to each chapter's first paragraph
  using `ref = (idx % n_refs) + 1`; `check_evidence_cited` passes on the presence of
  a `cite` class token. A citation need not support its claim.
* **Evidence.** pipeline audit 21; `factory.py:444-478`; `quality_gates.py` gate I.
* **Implementation.** (1) Delete the modulo; (2) require every `science`/claim block
  to carry `evidence_ids[]` ∈ the chapter's slot set; (3) render one anchor per
  distinct id and assert count parity in a gate; (4) add
  `ipf/authoring/evidence_audit.py` performing one batched entailment call per
  chapter (`SUPPORTED | PARTIAL | OVERCLAIM | UNSUPPORTED`), with `UNSUPPORTED`
  failing hard and `PARTIAL` allowed only when the claim is hedged (deterministic
  check on `likely|tends to|may|in most cases`); (5) resolve every evidence URL
  (HEAD/GET with a timeout) and fail the gate on 404 — the repo already has the
  fetching idiom in `runs/run_book_research.py`.
* **Reuse.** the atomic-claim → entailment → support-ratio metric in
  `ragas/metrics/_faithfulness.py:134` (Apache-2.0) as the *pattern*; do not import
  the package.
* **Expected benefit.** Makes the factory's core differentiator true rather than
  asserted.
* **Dependencies.** none.
* **Risks.** An entailment model can be confidently wrong → mitigate by storing the
  verdict *and* the quoted pair, and by including entailment accuracy in the
  golden-set human spot-check.
* **Testing.** Unit: a claim citing an unrelated reference is `UNSUPPORTED`; a
  hedged partial is allowed; a science block with no ids fails; anchor count equals
  distinct id count. Golden: a hand-labelled set of 30 claim/evidence pairs with an
  agreed verdict.
* **Acceptance criteria.** No rendered product contains a citation whose
  `evidence_id` is absent from its chapter's slot set; the gate fails on a
  deliberately mis-linked pair; `inject_citations` contains no `%` operator.
* **Metric.** *Citation fidelity* = % of sampled claims rated SUPPORTED by a human
  spot-check (target ≥ 95 %; today the quantity is undefined because citations are
  positional).

### R-03 — Wire the semantic critic into the product path

* **Problem.** `ipf/products/factory.py` imports `ipf.authoring.quality_gates` but
  **no module under `ipf/products/` or `ipf/authoring/` imports `ipf/writing/**`**.
  The critic machinery (`editorial/criteria.py` — "does this WORK?" —
  `editorial/benchmark.py`, `editorial/calibration.py`, `evaluators.py` with
  `BlindEvaluation`/`CorpusAnchorEvaluator`/`StyleCriticEvaluator`) is used only by
  `runs/*`.
* **Evidence.** pipeline audit 30; `grep` for `import.*writing` under
  `ipf/products`/`ipf/authoring` returns nothing.
* **Implementation.** In `ProductFactory.execute`, after `run_reviewers`, call a new
  `ipf/authoring/critic.py` that (a) runs the existing editorial criteria over the
  rendered book, (b) makes one adversarial-editor call per book (prompt in
  Deliverable 6 §3 R8), and (c) requires quoted findings for any non-PASS verdict.
  Fold the result into `qa_report.json` as `critic`, and let it *fail* the pass
  condition. Bound revisions to 2 re-runs of the failing chapters.
* **Reuse.** the repo's own lab (no new dependency); optionally DeepEval's
  `assert_test`/`LLMTestCase` *shape* for threshold gates (Apache-2.0).
* **Expected benefit.** Turns "green gates on a 1,030-word book" into a judgement.
* **Dependencies.** R-05 to calibrate the critic against humans.
* **Risks.** The critic becomes a rubber stamp or an over-blocker → measure
  precision/recall of critic verdicts against human ratings on the golden set and
  publish the numbers.
* **Testing.** Unit: a bland-but-valid book is `REVISE`; a book whose chapters each
  name the behaviour they change is `PASS`; the verdict is persisted in QA.
* **Acceptance criteria.** `qa_report.json` contains `critic.findings[]` with quoted
  text; a fixture that passes today's gates but contains no actionable content is
  `REVISE`.
* **Metric.** *Gate precision* = share of `PASS` verdicts a human agrees with
  (target ≥ 0.8); *findings usefulness* = share of findings a human marks
  "actionable" (target ≥ 0.7).

### R-04 — Un-dead-end the control plane

* **Problem.** Shark's `differentiation` criterion requires `mechanism` ≥ 8 words;
  `differentiation` is one of the four `ESSENTIAL` criteria; nothing ever writes
  `mechanism`, `competition_summary` or `feasibility`; `ProductFactory.validate`
  requires a `PASS`. **No real opportunity can be produced.** The only persisted
  product was built from test-fixture evidence.
* **Evidence.** pipeline audit 7, 8, 9, 10; `opportunity_research.py:273-279`;
  `shark_rubric.py:46-47,131-134,116-122`; `factory.py:42,198-204`. All cited line
  numbers were re-verified against committed HEAD `03bf3b5`.
* **Implementation.** Choose explicitly and record the choice:
  *(a)* produce the three fields — `mechanism` from the transformation spec's
  action (R-01), `competition_summary` from a grounded competitor scan that must
  name artefacts or return `INSUFFICIENT`, `feasibility` from the factory's own
  constraints; **or** *(b)* relax the Shark rubric so unproducible criteria are
  `MISSING` and cannot be `ESSENTIAL`, and change the production prerequisite to
  `PASS or REVISE-with-no-blocking-findings`. Option (a) is preferred; option (b)
  must be accompanied by removing the misleading criteria from the rubric rather
  than leaving them permanently unmet.
* **Reuse.** the existing `SharkRubric` field-binding discipline (every criterion
  judged from a named field; MISSING ≠ 0.5) — that design is already right.
* **Expected benefit.** Every other improvement becomes end-to-end testable.
* **Dependencies.** R-01 gives `mechanism` a natural source.
* **Risks.** Relaxing the gate would lower the bar → mitigate by keeping
  `demand_severity_evidence` blocking and by adding the R-06 enforcement.
* **Testing.** An integration test that starts from a *real* curated archive and
  reaches a persisted `products` row with `gates_are_real=true` and a non-null
  `opportunity_id`.
* **Acceptance criteria.** One real opportunity traverses research → SELECT →
  Shark → PRODUCT_BUILD with no hand-seeded fields, and the resulting product's
  `brief.json` contains no fixture strings.
* **Metric.** *End-to-end success rate on real opportunities* (target: ≥ 1 of 3
  attempted; today structurally 0).

### R-05 — Golden-set evaluation harness

* **Problem.** Nothing in the repo compares a factory output against a baseline or
  a previous version. The writing lab has a blind, randomised, non-self-anchored
  protocol (`runs/run_heldout_experiment.py`, `ipf/writing/evaluators.py`) that has
  never been applied to a *product*.
* **Implementation.** `runs/run_product_benchmark.py` + `data/golden_set/`:
  ≥ 12 frozen briefs (niche, problem, painful moment, audience, evidence pack,
  page budget) committed *before* any change. For each brief, generate a baseline
  and a candidate. Anonymise, randomise order, and score with the rubric in §3.
  Include one **non-self anchor**: a strong general model given a plain
  "write a 20-page guide about X" prompt — the real alternative the buyer faces.
* **Reuse.** the repo's own evaluator classes; `promptfoo` (MIT) or `deepeval`
  (Apache-2.0) as an optional harness.
* **Dependencies.** none; it should land **before** R-01/R-03 are trusted.
* **Risks.** Evaluator drift and self-preference → mitigated by anonymisation,
  randomised order, at least one non-self anchor, and a human spot-check of a
  stratified sample.
* **Acceptance criteria.** One command produces `scores.csv` + a report with
  per-metric deltas, and the baseline is reproducible from the frozen briefs.
* **Metric (the harness itself).** Inter-rater agreement between two independent
  scorers on ≥ 20 outputs (target Spearman ρ ≥ 0.6); otherwise the rubric is
  rewritten.

### R-06 — Close the fabrication paths and enforce the demand gate

* **Problem.** `POST /products/demo/random` INSERTs a `COMPLETED` +
  `SYSTEM_VALIDATED` product with null ids and no QA, and it is visible in
  `GET /products`; `CREATOR_ANALYSIS` stamps `ANALYZED` without analysing;
  `decide_opportunity` accepts `SELECT` on `demand_verdict=PROBLEM_REJECTED`;
  `SharkRubric` marks the blocking demand criterion `MET` on that same verdict.
* **Evidence.** infra audit F03, F06, F09, F10.
* **Implementation.** Gate the demo endpoint behind an explicit
  `IPF_ENABLE_DEMO_ENDPOINTS=1` and mark its rows `status='DEMO'` (never
  `COMPLETED`, never `SYSTEM_VALIDATED`); make `CREATOR_ANALYSIS` fail as
  `INTEGRATION_UNAVAILABLE` until it does something, or implement it; enforce
  `demand_verdict == PASS` (or the red-team verdict) in `decide_opportunity` via
  `PolicyEngine` — which is currently imported by **zero** modules and should
  become the single enforcement owner; make Shark treat non-`PASS` demand as
  `MISSING`/`REJECT`, never `MET`.
* **Expected benefit.** The honesty doctrine becomes enforced by code rather than
  by convention.
* **Complexity.** S. **Risks.** Low. **Testing.** Regression tests asserting each
  endpoint either refuses or records the honest state; a test that `SELECT` on a
  rejected demand returns a machine-readable error.
* **Acceptance criteria.** `GET /products` cannot return a row whose
  `validation_level` is above what its QA supports; a rejected-demand opportunity
  cannot be selected; Shark's demand criterion cannot be `MET` without a `PASS`.

### R-07 — API auth hardening

* **Problem.** With `IPF_API_TOKEN` unset (the default) any local process can drive
  the factory, and `Access-Control-Allow-Origin: *` with no `Origin` check lets any
  web page in the operator's browser reach `http://127.0.0.1:8080/api/v1/*`.
* **Evidence.** infra audit F02.
* **Implementation.** Require a token when any mutating route is enabled; replace
  wildcard CORS with an allowlist (or none) and add an `Origin` check for
  non-GET; keep the loopback bind and keep refusing `0.0.0.0` (already the case).
* **Complexity.** S. **Risks.** Breaking the local UI → update `web/api.js` in the
  same change and fix the SSE query-token mismatch (infra audit F20) at the same time.
* **Acceptance criteria.** A cross-origin `POST` from a page origin is refused; a
  missing token yields 401 on every mutating route; SSE works with a token.

### R-08 — Research loader preference and evidence relevance

* **Problem.** `_load` prefers `problems_<niche>_threads.json`, whose clusters have
  `distinct_commenters` 1/0/1/0/0, over the keyword-search file with 12/12/9/8/6;
  and the money artifact's evidence links to `r/options` ("ruined my life from
  trading") and `r/steammachine` — semantically unrelated; 3 of 3 sampled refs are
  off-topic; `post_url` values are malformed double URLs.
* **Evidence.** pipeline audit 13, 14, 15; infra audit F19.
* **Implementation.** Prefer the artifact with the higher evidence density
  (distinct commenters/threads) and log which was chosen; add an assertion to the
  curation contract that each kept comment's cluster patterns match (already
  intended) and that `post_url` is a single valid URL, failing loudly otherwise.
* **Complexity.** S. **Acceptance criteria.** No artifact with a malformed URL is
  loadable; the loader's choice is recorded in the run artefact; a fixture with
  mismatched evidence fails curation.

---

## 3. P1 items (condensed)

| # | Item | Implementation sketch | Acceptance | Metric |
|---|---|---|---|---|
| R-09 | Deterministic suite; drop source-text assertions | Fix the 3–4 flaky failures; convert the ~40/118 source-text tests to behaviour tests or delete them (per repo convention: never re-pin incidental behaviour) | 3 consecutive runs with identical results | flake rate = 0 over 3 runs |
| R-10 | Niche descriptor as data | `ipf/research/niche_descriptor.py` + JSON descriptors; `NICHE_SPECS` becomes data | a 4th niche added with no Python edit | time-to-add-niche < 1 h |
| R-11 | Stage-level resumability | write `artifacts/products/<id>/<stage>.json` before the next stage; stage key `sha1(brief_version+stage)`; re-running a completed stage is a no-op | kill mid-authoring, restart, no chapter regenerated | LLM calls saved on resume |
| R-12 | Tool/worksheet model | `Tool` dataclass + deterministic validation + real markup rendering | every tool has fields, a scoring rule and a ties rule; renders to `<table>`/`<input>` | % chapters with a usable tool (target 100 % where declared) |
| R-13 | Offer stage | `OfferComposer` + `OfferGate` (no $ in promise, no superlatives, every claim maps to a chapter, monotone ladder) | a sales page whose every claim cites a chapter | claim→chapter coverage = 100 % |
| R-14 | PDF + EPUB3 export | exporter interface; WeasyPrint for PDF; in-house EPUB3 packager (`zipfile`+`xml.etree`: OCF `mimetype` stored-first, `META-INF/container.xml`, OPF 3 manifest/spine/properties, nav + NCX, `cover-image`); `UNAVAILABLE` when the binary is absent | `product.html/.pdf/.epub` from one model; anchors resolve in all three | 100 % of exports pass epubcheck |
| R-15 | Cost accounting | usage row per call (model, tokens, latency) + `cost_per_accepted_product` in the harness | no LLM call without a usage row | cost/artifact known (±10 %) |
| R-16 | SQLite thread safety | one connection per thread (or a lock) + explicit transactions; stop swallowing `init_db` exceptions | concurrency test with N writers | 0 `database is locked` under a 50-job test |
| R-17 | Doc/code truth | rewrite `API_CONTRACT.md`/`openapi.yaml` to match the 44 real routes; fix README's metric vocabulary claim | a doc test that asserts the route list matches | drift = 0 routes |
| R-18 | Dead-code removal | remove `CreatorScorer`, `EvidenceStore`, the unreachable `LATE` branch, and the unwired `reader_persona` preflight **or** wire it; decide `DpfBridge` (delete or implement) | `grep` for each symbol returns only removal history | LOC removed / dead branches = 0 |

---

## 4. P2 items

| # | Item | Why wait |
|---|---|---|
| R-19 | Whop/publish integration | Needs R-13 (an offer to publish) and a sandbox key; the capability registry already models `UNAVAILABLE` correctly, so there is no urgency. |
| R-20 | Image/asset generation | The stdlib gradient cover is honest and dependency-free; real imagery is cosmetic until R-01/R-02 change the content. |
| R-21 | epubcheck/ace in CI | Only meaningful once R-14 exists. |
| R-22 | SQLite FTS5 over evidence | Retrieval volume does not justify a vector database; FTS5 is in stdlib SQLite. Do this before considering embeddings. |

---

## 5. Validation experiments

### 5.1 The one experiment to run first

**Run R-05 before R-01.** Design:

* **Sample.** 12 frozen briefs from the *real* curated archive across ≥ 3 niches,
  plus 1 deliberately weak brief as a negative control. Briefs are committed and
  hashed before any change; a brief may never be added or edited during the
  experiment.
* **Arms.** (A) current factory (baseline, commit `0b2848f` or the then-HEAD);
  (B) candidate; (C) **non-self anchor** — a general model with a plain prompt
  ("write a practical 20-page guide for X, with worksheets"), i.e. the alternative
  the buyer actually has.
* **Blinding.** Outputs are renamed to random opaque ids, order randomised per
  brief and per scorer, and the scorer is not told which arm is which. Scoring
  happens in a fresh session with no access to the generator's code or notes.
* **Scorers.** two independent human scorers on all 13 briefs, plus one automated
  pass (R-03's critic) as a third data point — never as the sole scorer.
* **Budget parity.** ~22 calls per arm per brief; the anchor arm gets a comparable
  token budget so that "more compute" cannot masquerade as "better method".

### 5.2 Rubric (each 1–5, with the justifying quote required)

| Dimension | What a 5 looks like | Anti-gaming |
|---|---|---|
| **Evidence fidelity** | every claim's citation supports it | scored against the evidence pack, not the text's own confidence |
| **Milestone coverage** | every chapter advances a named transformation milestone | scored from the brief's milestone list, fixed in advance |
| **Actionability** | the reader can do the first action today without new tools | the scorer must name the action |
| **Specificity** | concrete instances; no sentence that could appear in any book on the topic | reuse `ipf/writing/editorial/specificity.py` as a *check*, not as the score |
| **Coherence** | milestones do not contradict; no repeated content across chapters | pairwise chapter overlap must be reported |
| **Tool usefulness** | the worksheet can be completed in <10 min and produces a decision | the scorer must attempt it |
| **Offer honesty** | no claim beyond the delivered transformation; no invented metrics | flag any number lacking a chapter source |
| **Finish** | the artefact is shippable after ≤30 min of formatting work | the scorer must either export it or state why not |

**Metrics additionally recorded (not scored):** time-to-usable (wall clock plus
declared manual-editing minutes), model cost per artefact, model calls, failure
rate, and export success.

### 5.3 Bias controls (mandatory)

1. **Never score length.** Length is recorded as metadata only; the report must
   state this explicitly.
2. **Never score confidence.** A confidently written wrong claim scores *lower* on
   evidence fidelity; the scorer is instructed that fluent hedging is not a defect.
3. Require a **quoted sentence** for every score, so a score cannot be given
   without evidence a second person can check.
4. Report **per-brief deltas** (a distribution), never a single average that hides
   a regression on one brief.
5. Declare the sample size and do not generalise beyond it; with n=13, treat the
   result as a directional signal, and require a metric to move by ≥ 1 scale point
   on a median before claiming improvement.
6. Re-run the harness unchanged after every later change; a metric that regresses
   blocks the merge (the repo's own gate-canary discipline, applied to products).

### 5.4 Acceptance thresholds for the first iteration

| Metric | Threshold to keep the change | Notes |
|---|---|---|
| Evidence fidelity (human) | ≥ 4.0 median, and **no** `UNSUPPORTED` claim in any output | the change must not merely move the average |
| Milestone coverage | 100 % of chapters | deterministic |
| Actionability | ≥ 3.5 median, and ≥ baseline | |
| Specificity | ≥ 3.5 median, > anchor arm | the anchor is the real competitor |
| Coherence | no cross-chapter contradiction found | deterministic overlap report + human |
| Cost per artefact | ≤ 3× baseline | guards against "quality by brute force" |
| Time-to-usable | ≤ 60 min including manual work | the competitor's own weakness is 2–10 h |
| Export success | 100 % for the formats attempted | |

**Explicit non-goal of the first experiment:** proving revenue impact. That
requires real buyers, and the repo's own `ValidationStatus` ladder already forbids
the factory from claiming beyond `PRODUCT_INTERNALLY_VALIDATED` without human
evidence. Do not edit that ladder to make an experiment look better.

---

## 6. Sequencing and the stopping rule

```
R-05 (harness) ─┬─> R-01 (outline) ──> R-04 (un-dead-end) ──> first real end-to-end product
                │                     │
R-06,R-07,R-08 ─┘                     ├─> R-02 (citations)
                                      ├─> R-03 (critic)
                                      └─> re-run harness -> decision gate
                                                 │
                                    pass ────────┴─────── fail
                                      │                     │
                              R-10..R-18 (P1)        fix the failing metric only,
                              R-19..R-22 (P2)        do not add scope
```

**Stopping rule:** if, after R-01/R-02/R-03, the harness shows no metric moving by
≥ 1 scale point on the median versus the baseline **and** the non-self anchor, stop
adding architecture. The correct conclusion in that case is that the bottleneck is
not the pipeline — it is the niche, the evidence quality, or the offer — and the
next work belongs in R-08/R-13, not in more agents.

---

## 7. Risk register for the programme itself

| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| Building agents for their own sake | High (industry default) | High | The design fixes the count at 6 roles; any addition needs a failing harness metric |
| Optimising against a self-authored rubric | Medium | High | Non-self anchor arm every run; human spot-check; no length/confidence scoring |
| Quality by brute force (cost explosion) | Medium | Medium | ≤3× cost cap as an acceptance threshold; premium models restricted to 4 artefacts |
| The concurrent editor invalidating the audit | Medium | Medium | Line numbers are re-verified before edits; every change lands with its own test |
| A measured improvement that does not sell | Medium | High | R-13 (offer) + R-19 (delivery) exist precisely for this; do not defer them past the first passing harness run |
| Over-refactoring working code | Medium | Medium | P0 items are additive (new modules + wiring) except R-02's deletion of the modulo, which is a 3-line change |
