# Proposed agent & skill architecture for the e-book / digital-product factory

**Author:** lead researcher · **Date:** 2026-10-11
**Status:** design proposal. Nothing here is implemented, and nothing here is
claimed to be *Synthesise AI's* architecture. Where a design element was
*informed by* an observed Synthesise behaviour, the evidence id from
`raw/lead_primary_evidence.md` is cited and the element is labelled `INFORMED BY`.
Everything else is `ORIGINAL DESIGN` derived from the weaknesses found in
`factory_gap_analysis.md`.

---

## 0. Design constraints taken from the real codebase

These are non-negotiable, because they are properties the existing repo already
has and that must not be broken by any addition.

| Constraint | Where it comes from | Consequence for the design |
|---|---|---|
| Application code is **stdlib-only Python** | `ARCHITECTURE_AUDIT.md` §5.6; `ipf/writing/model.py` uses `urllib.request` | New stages must not add a runtime dependency to `ipf/`. Heavy libraries live in `tools/` behind adapters, or in a separate service. |
| One authority per concern | `ipf/orchestration/state_machine.py` is the only transition owner; `ipf/domain/json_store.py` is the one store reader; `ipf/products/factory.py::persist_product` is the one product writer | Each new stage exposes a pure function + a dataclass; it never writes state directly, it returns a value the existing owners persist. |
| **Honesty over green** | `NO_DATA ≠ 0` (metrics), `INSUFFICIENT_EVIDENCE > PASS` (Shark), refusal on missing evidence (`DemandValidator`), "never fake" writer | Every stage returns `status ∈ {OK, INSUFFICIENT_INPUT, DEGRADED, FAILED}` and never substitutes a plausible default for a missing fact. |
| No fabricated facts | `FORBIDDEN_EVIDENCE_OVERCLAIMS`, `check_no_fabricated_experience`, `scan_for_fabrication` | Evidence binding is a first-class artefact, not a prompt instruction. |
| Deterministic where possible | the whole `scoring/` layer, `Determinism` in DPF | Anything a regex/rubric can decide is not an LLM call. |

**The single most important design decision in this document:** the factory
currently has a *content* pipeline and a *quality* pipeline that do not touch
each other. The proposal below connects them by making the outline, not the
prompt, the contract.

### 0.1 Two preconditions this design assumes (from `factory_gap_analysis.md`)

1. **An enforcement owner.** The policy layer is currently orphaned:
   `ipf/domain/policies.py` is imported by **zero** modules, `PolicyEngine`,
   `Recovery`, `Supervisor`, `DealLock` and `LearningStore` are instantiated only in
   tests, and the demand gate is bypassable — `decide_opportunity` accepted a
   `SELECT` on an opportunity whose own `demand_verdict` was `PROBLEM_REJECTED`
   (verified live). **Every rule added below must have exactly one enforcement call
   site, and that call site should be `PolicyEngine`.** A rule that lives only in a
   prompt or a docstring is not a rule.
2. **No fabrication paths.** `POST /products/demo/random` INSERTs a `COMPLETED` +
   `SYSTEM_VALIDATED` product with null creator/opportunity ids and no QA, and
   `CREATOR_ANALYSIS` stamps `ANALYZED` without analysing anything. Any stage added
   here must be structurally unable to reach a terminal state without evidence —
   the roles below return `INSUFFICIENT_INPUT`/`DEGRADED` instead — and the roadmap
   closes both paths (R-06) before new stages are trusted.

---

## 1. What is actually missing (and therefore what the roles must do)

Derived from `factory_gap_analysis.md` (file:line evidence there):

1. **The structure of a product is hardcoded, not derived.** `ipf/products/factory.py`
   `compose_chapters` holds a fixed 8-item `plan` list. Every product gets the
   same 8 chapters regardless of topic, reader, or transformation.
2. **Research is niche-hardcoded.** `runs/run_book_research.py` `NICHE_SPECS` is a
   literal dict of three niches with hand-written keyword clusters.
3. **Citations are decorative.** `inject_citations` links chapter *i* to
   reference `(i % n_refs) + 1`. A citation can therefore point at an unrelated
   source while `check_evidence_cited` passes on the presence of `class="cite"`.
4. **QA measures presence, not value.** `run_all_gates` is markup/regex
   heuristics (`check_value_mechanism` literally tests `"mechanism" in text`).
   The repo *owns* a semantic evaluator (`ipf/writing/editorial/*`,
   `ipf/writing/evaluators.py`) but the product path never imports it
   (verified: `ipf/products/*` has no `import … writing`).
5. **No offer stage.** There is no positioning, price, promise, or sales-asset
   artefact anywhere in the product pipeline.
6. **Three disjoint memories** (`authoring_memory`, `editorial_memory`,
   `writing_memory`) with no product-level record of *what was built and how it
   scored*.
7. **Export is HTML only** (`EbookBuilder`/`LongBookBuilder` → `product.html`).

---

## 2. The architecture in one picture

```mermaid
flowchart TD
  subgraph DET["Deterministic (no model)"]
    A[OpportunityScout<br/>fetch + cluster]
    B[EvidenceCurator<br/>strict filter]
    G[StageGates<br/>contract checks]
    J[Renderer<br/>HTML → PDF/EPUB]
    K[GoldenSetEvaluator<br/>rubrics + blind A/B]
  end
  subgraph LLM["Model roles (structured output)"]
    C[ReaderModeler]
    D[OutlineArchitect]
    E[ChapterAuthor]
    F[ToolDesigner]
    H[EditorialCritic]
    I[EvidenceAuditor]
    L[OfferComposer]
  end
  A --> B --> C --> D --> G
  G -->|OK| E
  E --> F --> H --> I
  H -->|revise| E
  I -->|citation mismatch| E
  I -->|OK| L --> J --> K
  K -->|regression found| H
  D -.->|OutlineContract| E
  D -.->|OutlineContract| F
  C -.->|TransformationSpec| D
  B -.->|Evidence[]| E
  B -.->|Evidence[]| I
```

Data contracts (one line each, all pydantic-shaped dictionaries today, JSON in
`artifacts/`), in dependency order:

```
Opportunity    {id, niche, problem_statement, painful_moment, audience, evidence_refs[], contrary_evidence[], status}
Evidence       {id, url, title, publisher, fetched_at, quote, quote_locator, niche_signals[], cluster}
ReaderProfile  {who, current_state, desired_state, constraints[], objections[], jargon_level, reading_context}
Transformation {from_state, to_state, milestones[{n, moment, observable_change, failure_mode}], proof_of_progress}
Outline        {title, promise, parts[{n, title, reader_outcome}], chapters[{n, title, objective, covers_milestone, evidence_slots[], deliverable, word_budget}]}
Chapter        {n, title, blocks[{kind, text|items|rows, evidence_ids[]}], word_count, self_check}
Tool           {kind: worksheet|checklist|tracker|route, purpose, fields[], scoring_rule, ties_rule}
Critique       {findings[{severity, criterion, quote, why, fix}], verdict: PASS|REVISE|FAIL|INSUFFICIENT}
ClaimMap       {claim_id, text, evidence_ids[], support: SUPPORTED|PARTIAL|UNSUPPORTED|OVERCLAIM}
Offer          {positioning, promise, price_ladder[{tier, price, includes[]}], objections[], sales_page_blocks[]}
```

---

## 3. The roles

Each role states: purpose, inputs, outputs, tools, prompt shape, quality
criteria, failure conditions, handoff, when to invoke / not invoke, and cost
profile. Prompts are **our own** and are written as implementation-ready
templates.

---

### R1 — `OpportunityScout` — DETERMINISTIC FUNCTION (not an agent)

* **Purpose.** Turn a niche into a ranked set of evidence-backed problem
  opportunities.
* **Inputs.** `{niche_label, source_allowlist[], max_threads, min_body_chars}`.
* **Outputs.** `Opportunity[]` + `Evidence[]` whose every row carries
  `url`, `fetched_at`, `quote`, `quote_locator`.
* **Change from today.** `NICHE_SPECS` (hardcoded) becomes a **niche
  descriptor** supplied as data: `{label, seed_queries[], core_terms[],
  pain_phrases[], cluster_rules[]}`. Keep the strict curation contract from
  `runs/curate_book_evidence.py` verbatim — it is the best part of the current
  design.
* **Invoke when.** A new niche is opened. **Never** per product.
* **Cost.** $0 (HTTP + regex only).

```python
# ipf/research/niche_descriptor.py (new, deterministic)
@dataclass(frozen=True)
class NicheDescriptor:
    label: str
    seed_queries: tuple[str, ...]
    core_terms: tuple[str, ...]      # word-boundary matched
    pain_phrases: tuple[str, ...]    # failure-cost language
    cluster_rules: tuple[tuple[str, str, tuple[str, ...]], ...]
    def validate(self) -> tuple[bool, str]: ...   # refuses empty sets
```

---

### R2 — `EvidenceCurator` — DETERMINISTIC + optional LLM label pass

* **Purpose.** Keep the loose fetch superset from becoming "evidence".
* **Inputs.** raw records; `NicheDescriptor`.
* **Outputs.** `Evidence[]`, each with a **verbatim quote** and a resolvable
  locator. Reject-upstream: nothing enters the pool without a URL.
* **Rule (inherited, keep).** a comment is evidence only if it is ≥ 90 chars,
  from a real account, matches ≥ 1 core term on a word boundary, **and** ≥ 1 pain
  phrase. A cluster count means "kept comments raising this problem".
* **LLM use (optional, cheap).** one batched call per 50 records to label
  `{relevance 0-3, cluster}` with a JSON schema; a record the model cannot map to
  a descriptor cluster is `UNKNOWN`, never forced into a cluster.
* **Quality criteria.** 100 % of rows have `url` + `quote_locator`; ≥ 90 % spot
  checks reproduce the quote from the URL.
* **Failure.** `INSUFFICIENT_INPUT` if the kept pool for any candidate problem
  is < 25 comments / < 10 distinct authors — the honest floor the current
  `DemandValidator.evidence_complete` already implies.

---

### R3 — `ReaderModeler` — LLM ROLE (structured)

* **Purpose.** Convert raw evidence into a falsifiable model of the reader and
  the transformation the product promises.
* **Inputs.** `Evidence[]` (with quotes), `Opportunity`.
* **Outputs.** `ReaderProfile`, `Transformation`.
* **Prompt (ours):**

```
SYSTEM
You are a reader-modelling analyst. You output JSON only, matching the schema
exactly. You never invent facts. Every claim you make about the reader must be
traceable to a supplied evidence quote; if the evidence does not support a field,
set that field to null and add it to "unknowns". A confident guess is a defect.

SCHEMA (abridged)
ReaderProfile{ who, current_state, desired_state, constraints[], objections[],
               jargon_level: casual|professional|technical, reading_context,
               unknowns[] }
Transformation{ from_state, to_state,
                milestones[{n, moment, observable_change, failure_mode, evidence_ids[]}],
                proof_of_progress }

USER
PROBLEM: {problem_statement}
PAINFUL MOMENT (verbatim from evidence): {painful_moment}
EVIDENCE (id | quote):
{e_1 | "…"}
{e_2 | "…"}
…
Write 3-5 milestones. Each milestone must be an OBSERVABLE change in the reader's
behaviour or state ("they stop re-reading the same page"), not a topic
("understanding budgeting"). Each milestone must name the concrete failure mode
that usually stops people at that point, taken from the evidence where possible.
Mark any field you cannot ground in evidence as null and list it in unknowns.
```

* **Quality criteria.**
  * ≥ 3 milestones; each `observable_change` contains a verb, an object, and an
    observable outcome (deterministic check: verb list + not-a-noun-phrase).
  * every `failure_mode` has ≥ 1 `evidence_id` or is listed in `unknowns`.
  * no milestone may be a restatement of the chapter title.
* **Invoke when.** Once per product. **Not** per chapter.
* **Cost.** One call, ~1.5k in / 700 out. Premium model (this artefact
  determines everything downstream).

---

### R4 — `OutlineArchitect` — LLM ROLE (structured)

* **Purpose.** Turn `Transformation` into a chapter plan whose **structure is
  derived from the milestones**, with evidence slots pre-bound.
* **Inputs.** `Transformation`, `ReaderProfile`, `Evidence[]`, `Evidence Map`
  constraint, page budget.
* **Outputs.** `Outline`.
* **The change that matters.** Today `compose_chapters` has a fixed 8-item plan
  and the LLM is only asked to *write prose into it*. Here the LLM produces the
  plan, and a deterministic gate validates the plan before any prose is written —
  which is the cheapest possible place to fail.
* **Prompt (ours):**

```
SYSTEM
You are an instructional designer. You output JSON only. Structure is derived
from the supplied transformation milestones — one part per milestone, in order,
and every chapter must serve exactly one milestone. You must not invent
structure that the milestones do not imply, and you must not add a chapter whose
reader_outcome is not a step on the path from from_state to to_state.

SCHEMA
Outline{ title, promise, parts[{n,title,reader_outcome,milestone_n}],
         chapters[{n,title,objective,covers_milestone,evidence_slots[],
                   deliverable,word_budget}] }

RULES
- 5-13 chapters; total word_budget must fit {max_words}.
- For each chapter set evidence_slots to the ids of supplied evidence that the
  chapter is entitled to cite. You may leave a chapter with [] — an unsupported
  chapter is honest; a fabricated one is not.
- deliverable is a concrete artefact the reader ends with (worksheet, checklist,
  routing grid, tracker, plan, script). At least {min_tools} chapters must carry
  a deliverable that is not "read this chapter".
- The chapter that handles the failure mode of milestone 1 must come after the
  chapter that teaches milestone 1.
```

* **Deterministic validation gate (`OutlineGate`, pure function, no model).**
  1. every `covers_milestone` ∈ milestone set; each milestone covered ≥ once;
  2. `sum(word_budget) ≤ max_words` and ≥ `0.7 × max_words` (a plan that cannot
     fill the book is a plan defect);
  3. every `evidence_slots` id exists in `Evidence[]`;
  4. ≥ `min_tools` chapters with non-null deliverable;
  5. no duplicate titles; no chapter objective that is a substring of another.
  *Failure ⇒ regenerate once with the failure list appended, then
  `INSUFFICIENT_INPUT`.*
* **Cost.** One call, ~2.5k in / 1.5k out, premium model, up to 2 attempts.

---

### R5 — `ChapterAuthor` — LLM ROLE (per chapter, the only fan-out)

* **Purpose.** Write one chapter against an explicit contract and an explicit
  evidence slot set.
* **Inputs.** `Outline`, `Chapter`, `ReaderProfile`, the chapter's bound
  `Evidence[]` (verbatim quotes), the writing policy (see R8), and the previous
  chapter's final paragraph (for continuity only).
* **Outputs.** `Chapter.blocks[]` where every science/claim block carries
  `evidence_ids[]`.
* **Prompt (ours):**

```
SYSTEM
You are the author of one chapter of a practical guide. You write for this
specific reader, described below. Rules:
- Write 350-500 words in 3-6 paragraphs. Plain human voice. No marketing.
- You may cite ONLY the evidence supplied. Every factual, statistical or
  research claim must be attributable to one of the supplied evidence ids, and
  you must emit it as a {"kind":"science","text":…,"evidence_ids":["e_7"]} block.
  If you want to make a factual claim you cannot attribute, do not make it.
- Never invent personal experience, testimonials, reader quotes or results.
- Open with the reader's situation, not with a definition. Close with exactly
  one action the reader can take today.
- No stock phrases: "in today's world", "it is important to note", "the good
  news is", "let's dive in", "at the end of the day".
- Vary sentence and paragraph length by purpose. Include one real tradeoff or
  condition ("this fails when…").

USER
READER: {who} | currently: {current_state} | wants: {desired_state}
MILESTONE THIS CHAPTER SERVES: {milestone_n}: {observable_change}
CHAPTER OBJECTIVE: {objective}
DELIVERABLE THE READER ENDS WITH: {deliverable}
PREVIOUS CHAPTER ENDED: "{last_paragraph}"   (continue, do not summarise)
SUPPLIED EVIDENCE (cite by id):
e_7 | {source, year} | "{verbatim quote}"  — usable for: {what it actually supports}
OUTPUT: JSON {"blocks":[…]} using kinds paragraph|bullet|checklist|callout|science|worksheet
```

* **Quality criteria.** word budget ±15 %; ≥ 1 tradeoff/condition marker; no
  banned phrase; ≥ 1 block with `evidence_ids` when `evidence_slots` is
  non-empty; **no `evidence_ids` outside the chapter's slot set** (hard fail).
* **Invoke when.** Fan-out is allowed here and only here. **Not** for
  worksheets, not for the offer, not for the outline.
* **Cost.** N chapters × (~2k in / 600 out). Use the cheap bulk model. Cache the
  evidence block across chapters; do not resend the full book.

---

### R6 — `ToolDesigner` — LLM ROLE (structured, small)

* **Purpose.** Produce a **usable** companion tool (worksheet / routing grid /
  tracker / checklist) tied to a milestone, so the book is not prose-only.
* **Inputs.** `Chapter.deliverable`, `Transformation.milestones[n]`, evidence-free.
* **Outputs.** `Tool` → rendered deterministically by the existing renderer.
* **Prompt (ours):**

```
SYSTEM
You design one reusable tool. Output JSON only:
Tool{kind, purpose, fields[{label, type: text|number|choice, options[]}],
     scoring_rule, ties_rule, output}
RULES
- The tool must be usable with a pen in under ten minutes.
- The scoring_rule must be explicit arithmetic or an explicit mapping, never
  "your judgement".
- If two answers tie, ties_rule must say what to do.
- Never ask the reader to record a number they cannot obtain in one day.
```

* **Quality criteria (deterministic).** every `choice` field has ≥ 2 options;
  `scoring_rule` is a machine-checkable expression or an explicit mapping table;
  `ties_rule` non-empty; the tool renders to real markup (`<table>`/`<input>`)
  so the existing `check_worksheet_or_tool` gate can be tightened from
  "has a class named worksheet" to "the tool's `fields` match the rendered DOM".
* **Cost.** One small call per tool, cheap model.

---

### R7 — `EvidenceAuditor` — DETERMINISTIC + LLM ADJUDICATION (the new gate)

* **Purpose.** **Replace positional citation with claim-level binding.** This is
  the single highest-value change in the whole document.
* **Inputs.** rendered `Chapter`s, their `evidence_ids`, the `Evidence[]` quotes.
* **Deterministic half.** Walk every `{kind: science|claim}` block:
  * every `evidence_ids` entry must exist and be in the chapter's slot set;
  * the rendered `<a class="cite" href="#r-7">` anchor count per chapter must
    equal the number of distinct `evidence_ids` the chapter declares;
  * **delete `inject_citations`' modulo behaviour** — `(idx % n_refs) + 1` is
    what makes a green "evidence cited" gate meaningless.
* **LLM half.** One batched call per chapter, entailment-style:
  `{"claim": "…", "evidence_quote": "…"} → SUPPORTED | PARTIAL | OVERCLAIM | UNSUPPORTED`.
* **Rule.** any `UNSUPPORTED` ⇒ hard fail; `OVERCLAIM` ⇒ fail against
  `FORBIDDEN_EVIDENCE_OVERCLAIMS`-style list; `PARTIAL` ⇒ allowed only if the
  claim is hedged (`likely`, `tends to`) — enforced deterministically.
* **Outputs.** `ClaimMap`.
* **Cost.** One cheap call per chapter, ~1.2k in / 200 out.

---

### R8 — `EditorialCritic` — LLM ROLE + the repository's existing rubrics

* **Purpose.** Judge whether the draft *works*, not whether it is present.
  This is the role that is missing from the product path today even though the
  code for it exists.
* **Inputs.** rendered book, `ReaderProfile`, `Transformation`.
* **Reuse, do not rebuild.** `ipf/writing/editorial/criteria.py` (each criterion
  answers "does this WORK?") + `ipf/writing/auditor.py` (mechanical detectors) +
  `ipf/writing/principles.py` (validated corpus principles) +
  `ipf/writing/evaluators.py` (`BlindEvaluation`, `CorpusAnchorEvaluator`,
  `StyleCriticEvaluator`) + `ipf/writing/editorial/benchmark.py` + the held-out
  protocol in `runs/run_heldout_experiment.py`. These already implement blind,
  randomised, non-self-anchored evaluation with negative controls — a genuinely
  strong asset that is currently only used by the writing/teacher runner.
* **Outputs.** `Critique` with `verdict ∈ PASS|REVISE|FAIL|INSUFFICIENT`.
* **Prompt (ours, the model half only):**

```
SYSTEM
You are an adversarial editor. You are paid to find the sentences that cost the
reader time and give nothing back. You judge EFFECT, not presence.
For each finding give: severity(blocker|major|minor), criterion, the exact quoted
text, why it fails for THIS reader, and the smallest fix.
You must return at least one finding if any of these hold: a paragraph that
could appear in any book on the topic; advice with no observable checkpoint;
a claim the reader cannot act on; a milestone whose failure mode is not handled;
rhetoric that replaces a mechanism. Returning "PASS" with no findings is allowed
only when you can name, for three chapters, the reader behaviour it changes.
```

* **Loop.** `REVISE` → re-run only the failing chapters with the findings
  appended (bounded: 2 revisions). `FAIL` → stop, persist the product as
  `NEEDS_REVISION` with the real finding list (the current behaviour).
* **Cost.** 1 call per book (+1 per revision round). Premium model.

---

### R9 — `OfferComposer` — LLM ROLE (structured) — **new capability**

* **Purpose.** Produce the commercial artefact the factory currently lacks:
  positioning, promise, price ladder, objection handling, and a sales-page
  outline — *derived from the same transformation*, not invented separately.
* **Inputs.** `Transformation`, `ReaderProfile`, `Outline`, `page_budget`.
* **Outputs.** `Offer`.
* **Prompt (ours):**

```
SYSTEM
You write the commercial framing for a product you did not write, using only the
transformation it actually delivers. Output JSON only. You must not claim any
result the milestones do not deliver, must not promise income, and must not
invent metrics, market-size numbers or testimonials.
SCHEMA Offer{positioning, promise, price_ladder[{tier,price,includes[]}],
             objections[{objection,answer}], sales_page_blocks[{kind,text}]}
RULES
- promise must be the to_state, in the reader's words, with no superlative.
- price_ladder must be justified by scope differences (what is included), never
  by "value $X" anchors. No fake strike-through prices.
- ≥ 5 objections taken from ReaderProfile.objections, each answered with a fact
  from the book.
- Every sales_page_blocks claim maps to a chapter number in {outline}.
```
* **Deterministic gate `OfferGate`.** no `$` figure in a `promise`; no
  superlatives in a promise (`best`, `world-class`, `guaranteed`, `proven`);
  every block declares `source_chapter`; price ladder monotone non-decreasing.
* **Cost.** 1 premium call per product.

---

### R10 — `Renderer` / `Exporter` — DETERMINISTIC PIPELINE

* **Purpose.** One canonical document model → HTML (exists), **PDF, EPUB, DOCX**.
* **Current state.** `LongBookBuilder`/`EbookBuilder` emit self-contained HTML,
  page-budget enforced, cover PNG generated with a hand-rolled zlib PNG writer
  (`_png_gradient`) — clever and dependency-free, but gradient-only.
* **Proposal.** Keep the HTML renderer as the single source of truth; add an
  exporter adapter with two backends behind one interface:
  * `pandoc` (or `weasyprint` for CSS-accurate PDF) as an **optional external
    tool**, invoked by an adapter in `ipf/tools/adapters/`, absent-safe
    (`UNAVAILABLE`, never a fake file);
  * a pure-stdlib fallback that emits a minimal, valid EPUB (zip + OPF + XHTML
    — `zipfile` is stdlib) so the capability never depends on a binary.
* **Acceptance.** page budget honoured in every format; citations resolve in
  PDF/EPUB (anchor ids preserved); cover is a real image; no format contains
  hidden methodology strings (`check_banned_user_facing` re-run per format).

---

### R11 — `GoldenSetEvaluator` — DETERMINISTIC HARNESS + HUMAN SPOT-CHECK

* **Purpose.** Make "the factory got better" a measurement, not a feeling.
* **Reuse.** the blind/randomised/non-self-anchored protocol already written in
  `runs/run_heldout_experiment.py` and `ipf/writing/evaluators.py`, lifted from
  *writing passages* to *whole products*.
* **Inputs.** a frozen set of ≥ 12 product briefs (niche, problem, audience,
  evidence pack) fixed before any change; baseline outputs; candidate outputs.
* **Outputs.** `scores.csv` + a report with per-metric deltas and CIs where the
  n permits.
* **Metrics** (rubric in `factory_improvement_roadmap.md` §Experiments):
  evidence fidelity, milestone coverage, actionability, specificity,
  coherence, tool usefulness, offer honesty, time-to-usable, cost/artifact.
* **Bias controls.** anonymise A/B; randomise order; at least one *non-self*
  anchor (a strong general model with a plain prompt, i.e. the real alternative
  the buyer faces); forbid length as a criterion; require the evaluator to quote
  the sentence that justifies each score.
* **Invoke when.** At any change to a prompt, model, outline logic, or gate.
  Never in the per-product path.

---

## 4. What is deliberately NOT an agent

| Tempting "agent" | Why it is not one |
|---|---|
| Niche discovery | HTTP + regex + a data file. An LLM adds cost and variance to a step that must be reproducible. |
| Deduplication / idempotency | `normKey`/`dedup_key` already exist; LLMs are non-deterministic and cannot be idempotent. |
| Page-budget / word-count control | arithmetic; `LongBookBuilder` already raises rather than fake. |
| Citation *resolution* | string/ID mapping. Only the *entailment judgement* needs a model. |
| Banned-language, fabrication, placeholder checks | regex lists already exist and are tested with negative-control canaries. |
| Profit / commission accounting | `partnerships/` + `Money` invariants; never a model. |
| "Orchestrator agent" | the state machine is authoritative and deterministic. A model choosing the next stage is a regression in reliability and auditability. |

**Agent-count claim:** this design needs **6 model roles**
(ReaderModeler, OutlineArchitect, ChapterAuthor, ToolDesigner, EditorialCritic,
OfferComposer) plus **2 adjudication/review calls** that are the same role used
differently (EvidenceAuditor, EditorialCritic-as-reviewer). It does **not**
need a swarm. Synthesise AI's own marketing claims 4–8 "agents"; its shipped
route table shows *stages of one pipeline* (E10b), which is exactly the shape
recommended here.

---

## 5. Cost-control strategy (explicit budgets)

| Control | Rule |
|---|---|
| Model tiering | premium model ONLY for `ReaderModeler`, `OutlineArchitect`, `EditorialCritic`, `OfferComposer`. Bulk model for `ChapterAuthor`, `ToolDesigner`, `EvidenceAuditor`. Never premium inside a fan-out. |
| Fan-out cap | one model call per chapter; no per-paragraph, no per-section, no per-tool-variant calls. |
| Context economy | evidence block is assembled once per product and referenced by id; chapters receive only their own slot set plus the previous paragraph. Never send the whole book to write a chapter. |
| Attempt cap | 2 attempts per structured artefact, then `INSUFFICIENT_INPUT`. No unbounded retry loops. |
| Deterministic-first | if a regex or a rubric can decide, no call is made. This is why the gate layer runs *before* authoring (cheap place to fail). |
| Caching | `Evidence[]` and `ReaderProfile` are content-addressed (`sha1` of canonical JSON) and reused across products in the same niche — one niche analysis can serve N products, mirroring the repo's existing "one problem → one product, N creators as affiliates" doctrine. |
| Degradation honesty | if the bulk model is unreachable, the product is `DEGRADED` with the templated fallback **and** `authoring_mode=DETERMINISTIC` recorded — the current behaviour, which is correct and must be preserved. |
| Budget accounting | every call writes a usage row with tokens and model id (the DPF already does this); the evaluator reports cost per *accepted* product, not per attempt. |

**Estimated call count per product (10–12 chapters):**
2 premium (reader, outline) + 1 premium (critic) + 1 premium (offer)
+ 12 bulk (chapters) + 3 bulk (tools) + 3 bulk (evidence audit)
≈ **22 calls**, of which 10 bulk pass over ~2k tokens. That is roughly the same
order as today's 8 calls, with the money moved from bulk volume to the two
artefacts that determine structure and judged quality.

---

## 6. Failure handling and resumability (contracts)

* Every role returns `(value, status, reason)`; `status` is one of
  `OK / INSUFFICIENT_INPUT / DEGRADED / FAILED`.
* A role failure does **not** silently downgrade a downstream artefact: the
  product is persisted at the stage that failed with `error_code`, exactly as
  `persist_product` does today.
* Stage outputs are written to `artifacts/products/<product_id>/<stage>.json`
  **before** the next stage runs, so a crash resumes at stage granularity. This
  is the one place the current design is weakest: `execute()` writes brief,
  chapters, product and qa at the end, so a failure in QA loses nothing but a
  failure mid-authoring loses the chapters.
* Idempotency: the stage key is `sha1(brief_version + stage_name)`; re-running a
  completed stage is a no-op that returns the persisted artefact.

---

## 7. Why this beats the current pipeline (mapped to evidence)

| Weakness (file:line) | Role that fixes it | How you can tell it worked |
|---|---|---|
| fixed 8-chapter plan (`ipf/products/factory.py::compose_chapters`) | R4 OutlineArchitect + OutlineGate | chapters differ per brief and every chapter maps to a milestone |
| positional citations (`inject_citations`) | R7 EvidenceAuditor | anchors per chapter == declared evidence ids; no `% n_refs` |
| presence-only gates (`ipf/authoring/quality_gates.py`) | R8 EditorialCritic using the existing unwired `ipf/writing/editorial/*` | findings quote text and name a reader-cost; PASS requires naming 3 behaviours changed |
| niche-hardcoded research (`runs/run_book_research.py::NICHE_SPECS`) | R1 niche descriptor as data | a 4th niche is added without editing Python |
| three disjoint memories | product-level `StageRecord` + reuse of `evidence` cache | one query answers "what did we build, from what sources, scoring what" |
| HTML-only export | R10 exporter interface | product.html + product.pdf + product.epub from one model |
| no offer artefact | R9 OfferComposer + OfferGate | a sales page whose claims each cite a chapter |

---

## 8. Implementation order (dependency-true)

1. **Outline-first** (R3, R4, OutlineGate) — replaces `compose_chapters`'s plan.
2. **Citation binding** (R7 deterministic half + deletion of the modulo
   behaviour) — makes the existing evidence gate meaningful.
3. **Wire the critic** (R8) into `ProductFactory.execute` after `run_reviewers`.
4. **Niche descriptor as data** (R1/R2 refactor) — low risk, unblocks new niches.
5. **Offer stage** (R9) — new artefact, no coupling.
6. **Exporter** (R10) — additive adapter.
7. **Golden-set harness** (R11) BEFORE 5–6 are trusted; ideally before 1.

Steps 1–3 are P0 in `factory_improvement_roadmap.md`; 4–6 are P1; 7 is P0 for
credibility even though it ships nothing user-visible.

---

## 9. Explicit non-goals

* No multi-agent "swarm", no autonomous self-improvement loop that changes its
  own prompts without a golden-set gate.
* No model in the money path, the policy path, or the state machine.
* No reproduction of any Synthesise AI prompt or interface: nothing in §3 is
  derived from their code, which was never available. The only `INFORMED BY`
  items are structural facts observed in the shipped route table (E10b):
  staged project artefacts, a per-artefact transformation map, ingestion-based
  expertise capture, and separate per-format pipelines.
