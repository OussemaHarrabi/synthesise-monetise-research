# Synthesise AI / Monetise / digital-product-factory — research repository

**Compiled 2026-10-11.** Evidence-driven dossier on Synthesise AI (Iman Gadzhi's
AI digital-product tool), the wider Monetise / Educate.io ecosystem, the
open-source building blocks worth reusing, and a code-backed gap analysis of the
local `ipf-factory` repository.

## Deliverables

| # | File | What it answers |
|---|---|---|
| 1 | `synthesise_monetise_dossier.md` | executive assessment, ecosystem and business model, complete user journey, UVZ, artefacts, prompt/agent evidence, customer evidence, final implementation decision |
| 2 | `source_ledger.csv` | 243 sources with id, URL, publisher, date, access date, type, claims supported, credibility, limitations |
| 3 | `technical_architecture.md` | verified stack vs inference vs unknown; the rejected "documentation" finding |
| 4 | `opensource_repository_audit.md` | 43 repositories scored, 35 cloned and code-inspected, licence and maintenance findings |
| 5 | `factory_gap_analysis.md` | capability matrix, code-backed defects, honesty violations, top-10 risks |
| 6 | `proposed_agent_and_skill_architecture.md` | 6 model roles, 11 deterministic stages, original prompt templates, cost budget |
| 7 | `factory_improvement_roadmap.md` | P0/P1/P2 roadmap with acceptance criteria and the validation experiment |
| 8 | `research_limitations_and_unknowns.md` | access failures, contradiction register, open questions, next steps |

## Raw evidence (`raw/`)

Per-workstream source material, including the first-hand artefacts
(`lead_primary_evidence.md`), the five topic dossiers with their own CSV source
files, and the two factory audits with file:line evidence CSVs.

## Evidence rules used throughout

* Every significant claim is classified: **VERIFIED / OFFICIAL CLAIM /
  USER-REPORTED / INFERENCE / UNKNOWN / CONTRADICTED**, with a confidence rating.
* Public marketing claims are never presented as verified technical facts.
* Claims that could not be corroborated are listed as UNKNOWN, and the one
  document that asserted a stack contradicted by the deployment is marked
  CONTRADICTED and rejected.
* No prompt is attributed to the subject product: zero of its prompts are public.
* Nothing in the audited application was modified by this work.

## Notes

* `raw/yt/` (third-party YouTube transcripts) is git-ignored and not published.
* Prices, bundle contents and product names in this ecosystem change on a
  roughly quarterly launch cycle — every commercial claim here is dated.
