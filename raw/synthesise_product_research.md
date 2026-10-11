# Synthesise AI — Evidence-Driven Product / Commercial Intelligence Dossier

**Subject:** Synthesise AI (synthesise.ai / app.synthesise.ai), the AI digital-product-creation tool bundled inside Iman Gadzhi's Monetise program, sold on Whop.
**Prepared:** 2026-10-11 (all "as of" statements are dated to this day unless a snapshot date is given).
**Audience:** a developer who already runs an automated e-book / digital-product factory and wants to know which mechanisms are worth copying.
**Method:** ~19 distinct web searches + Wayback Machine snapshots + direct page fetches + YouTube transcript extraction (yt-dlp) + one parent-agent extraction of the publicly served client JS bundle. Every significant claim is classified **VERIFIED / OFFICIAL CLAIM / USER-REPORTED / INFERENCE / UNKNOWN / CONTRADICTED** with high/medium/low confidence and a source id (`S01`…) resolved in `synthesise_product_sources.csv`.

> **Access-limiting caveats (read first).**
> 1. `synthesise.ai` is a **Framer** marketing site; its FAQ accordion answers **do not render** as text in the served HTML/markdown/search-index (only the five question strings do). `[VERIFIED][high][S01,S02]` The FAQ answers are therefore **UNKNOWN**.
> 2. `whop.com/checkout/plan_wPgM4iy64m7Js` is a JS-only Whop checkout; no price/terms could be read from it. `[VERIFIED][high][S41]`
> 3. Reddit blocks all fetch paths I tried (native, `.json`, `old.reddit.com`, r.jina.ai — all HTTP 403/redirect-to-login). Reddit commentary is therefore only available second-hand through aggregators such as S30. `[VERIFIED][high][S30]`
> 4. The app itself (`app.synthesise.ai`) is login-gated behind Whop OAuth. All UI/journey evidence below comes from (a) third-party screen-recorded walkthroughs, (b) live-demo descriptions in a dated third-party review, (c) the publicly served client bundle. `[VERIFIED][high][S05,S19,S20,S21,S22,S23,S43]`

---

## KEY FINDINGS (20 bullets)

- **Synthesise AI is a two-part product: a Framer marketing site (`synthesise.ai`) plus a login-gated Next.js app on Vercel (`app.synthesise.ai`), authenticated through Whop OAuth.** *[VERIFIED][high][S01,S05,S43]* — the Sign-in screen literally reads "Log in using your Monetise account" and offers only "Sign in using Whop". *[VERIFIED][high][S05]*
- **The operator is IAG Services FZCO, doing business as Synthesise AI / Educate.io, registered at DSO-IFZA-20424, IFZA Properties, Dubai Silicon Oasis, Dubai, UAE; governing law UAE, DIAC arbitration in Dubai.** *[VERIFIED][high][S03,S04,S13]*
- **Live price as of 2026-10-11 is stated as "$2,999 / one-time payment" (unlimited credits, UVZ calculation, product-charter generation, premium support).** *[VERIFIED][high][S01,S02]* (verified as the published offer, not as a verified capability)
- **The same live site's Terms of Service say the service "operates on a monthly subscription basis", with a 7-day refund on the initial subscription only and cancellation required 48 h before renewal — which directly contradicts the "$2,999 one-time payment" line on the marketing page.** *[CONTRADICTED][high][S01,S03]*
- **Pricing history is real and messier than any single page admits: May-2025 site showed two annual tiers ($1,499 / $2,999 per year); Sep-2026 site shows a single $2,999 one-time price; and the vendor's own Monetise offer describes Synthesise AI as "$2,999 value, 12 months unlimited".** *[VERIFIED][high][S01,S11,S38]*
- **Synthesise AI is not sold as a standalone vendor product. Access is bundled inside Monetise ($1,995 one-time in Sept 2026, financing from $166/mo), with software access limited to 12 months and "subject to fair use".** *[VERIFIED][high][S11,S12,S23,S24,S40]*
- **A second, apparently unrelated Whop storefront also called "Synthesise AI" (location: Montréal, CA; account joined Jan 2025; 5 followers; $0 earned) sells "Synthesise AI — The Digital Product Generator" for $97/month.** *[VERIFIED][medium][S16,S17,S18]* (verified that the listing exists; its provenance is not) — whether it is authorised, a reseller, or an impersonation is **UNKNOWN**, and it is one of the clearest examples of brand confusion in this market. *[UNKNOWN][medium][S16,S17]*
- **UVZ (Unique Value Zone) generation is ingestion-based, not merely prompt-based: the served client bundle exposes tRPC procedures `flow.ingestInstagram`, `flow.ingestYoutube`, `flow.listYoutubeVideos`, `flow.parseGWOSAudit` alongside `flow.createFlow`.** *[VERIFIED][high][S43]* (via client-bundle extraction)
- **UVZ candidates are ranked with four named scored dimensions — pain, worsening, purchasing power and speed — each scored out of ten and summed.** *[USER-REPORTED][medium-high][S20,S23]* (two independent screen-recorded walkthroughs + one dated live-demo description)
- **The claimed "$3 billion in real sales data from Whop" training corpus is an unverifiable marketing claim; no dataset, methodology, model card or citation is published anywhere.** *[OFFICIAL CLAIM][low][S11]*
- **The claim that outputs are "synthesised from over 150 sources" with a reference list handed to the user was never demonstrated — the source list was not shown in the live demo, and no walkthrough ever surfaced a citation UI.** *[CONTRADICTED][low-medium][S23]* (the claim was made and then never shown)
- **Output formatting is the single most-criticised weakness: generated e-books are plain text with only titles bolded, no table of contents, no images/media, no proper WYSIWYG editor; two independent reviewers put the mandatory manual reformat/design work at "hours" per product.** *[USER-REPORTED][high][S19,S21]*
- **A countervailing hands-on review produced a 152-page e-book in ~30 minutes and judged it "good enough… to ship" as a lead magnet, faulting only the design.** *[USER-REPORTED][medium][S22]*
- **Observable generation bugs/limits: after a UVZ set is generated the user cannot edit the UVZ input or delete-and-restart cleanly; the run must be abandoned at product level; generations intermittently hang and need a page refresh; generated cover art contains spelling mistakes.** *[USER-REPORTED][medium-high][S19]*
- **Format picker is partially gated: ebook, video course and coaching offer are live; digital asset, lead magnet and presentation are greyed out behind "create a product to unlock", and the video-course/coaching-offer paths were said to require a Monetise Pro upgrade.** *[USER-REPORTED][medium][S19,S23]*
- **The pipeline terminates in a downloadable PDF (with margin settings) plus a "product charter" that is explicitly designed to be handed to the sibling tool Ghostwriter OS for sales copy — i.e. the product is one node in a two-tool funnel, not a complete publishing system.** *[USER-REPORTED][medium][S19,S20]*
- **Publishing to Whop is built in: the same screen generates a Whop store page, lets the user set a price, and publishes with one button (demo priced the artefact at $150, with $150–$300 framed as the target range).** *[USER-REPORTED][medium][S23]*
- **The GitBook "Synthesise AI" documentation is almost certainly not official first-party documentation and should not be treated as evidence of the real stack: it is not linked from synthesise.ai, all Rust/WASM/Postgres/Kubernetes "code samples" are decorative stubs, and it describes a `$SYNAI` pump.fun token with 95% liquidity that appears nowhere in any vendor page, filing or press release.** *[INFERENCE][medium-high][S06,S07,S08,S09,S10]*
- **No machine-learning component is verifiable from the client. "Machine learning", "cosine similarity" and "vectorized corpus" claims exist only in marketing copy and the unverified GitBook. The client contains no model-provider string.** *[UNKNOWN][medium][S01,S07,S43]*
- **Independent practitioners value the tool far below its $2,999 label — two reviewers put fair standalone value at roughly $100–$300/year, calling it "Iman's custom version of ChatGPT with pre-built prompts and templates".** *[USER-REPORTED][medium][S19,S21]*

---

## A. What Synthesise AI is (precise, one paragraph)

Synthesise AI is a **web application that turns a knowledge/niche input into a structured, downloadable digital product (primarily an e-book, with course and coaching variants), wrapped in a heavily-scored "Unique Value Zone" ideation front end**. It is operated by **IAG Services FZCO** (Dubai/UAE), marketed on a Framer site at `synthesise.ai`, and served from a Whop-authenticated Next.js app at `app.synthesise.ai`. It is **not sold standalone by the vendor** — access is provisioned through Iman Gadzhi's **Monetise** program (a $1,995 one-time purchase as of September 2026, delivered on Whop) with software access stated as **12 months**. The product's own marketing frames it as a "breakthrough tool developed and used by the world's foremost online creators" that "reduces months of complex ideation… to seconds of generation"; independent users consistently describe it instead as **a structured, prompt-scaffolded wrapper over general-purpose LLMs** whose distinguishing value is the *sequence* (UVZ → customer journey → existing solutions → transformation map → outline → worksheets), not the generative text quality. `[VERIFIED for the factual/entity claims — S01,S03,S05,S13; USER-REPORTED for the wrapper characterisation — S19,S21,S22,S23,S40]`

---

## B. Ownership / operator / launch timeline

| Date | Event | Evidence | Classification |
|---|---|---|---|
| 2024-05-19 → 2025-01-08 | `synthesise.ai` returns HTTP 302/308 (redirect/placeholder) — no product page. | Wayback CDX for `synthesise.ai*` `[S39]` | VERIFIED [high] |
| 2025-01-?? | Whop creator account `@synthesiseai` (Montréal, CA) reports "Joined Jan 2025"; this is **not** demonstrably the IAG entity. | `[S17]` | VERIFIED [medium] |
| 2025-01 | Monetise **1.0** launched at $999 / 12 months (software-first subscription; bundled Personal Brand Blueprint, **Synthesise AI**, Flozy Pro). | `[S24,S31,S34]` | VERIFIED [medium-high] |
| 2025-04-27 | `synthesise.ai` live as a **waitlist** page ("JOIN THE WAITLIST"); ecosystem logos then include **OpenAI** as well as Microsoft Founders Hub, Monetise, Educate. | Wayback `[S37]` | VERIFIED [high] |
| 2025-05-13 | `synthesise.ai` shows an actual **Pricing Plan: $1,499 / year and $2,999 / year** (both tiers still invite waitlist signup). | Wayback `[S38]` | VERIFIED [high] |
| 2025-05-09 | First widely-viewed third-party review of Synthesise AI ("Is it Worth $1500?"). | `[S20]` | VERIFIED [high] |
| 2025-09-29/30 | **Monetise 2.0** announced/launched during the free "Make Money Online Challenge 2.0" (Sep 28–30, 2025); press release names "Synthesize AI" as an included tool. | `[S32]` | VERIFIED [medium] (paid PR wire) |
| 2025-10-06/24 | `synthesise.ai` still a **waitlist** page. | Wayback `[S39]` | VERIFIED [high] |
| 2025-10-16 | Whop (a company Gadzhi co-owns since Apr 2025) publishes a case study describing Synthesise AI as "AI tool that finds profitable niches and builds your entire digital product for you". | `[S15,S23]` | VERIFIED [medium-high] |
| 2026-07-18 → 2026-09-09 | `synthesise.ai` relaunched with live pricing, FAQ and Whop checkout link; Framer `Last-Modified: Wed, 09 Sep 2026`. | `[S01,S02]` | VERIFIED [high] |
| 2026-09-07/08 | **Side Hustle Summit** (Sept 2026): Gadzhi demonstrates Synthesise AI live twice, revealing a previously undisclosed **Creator-audience mode**; cart opens at $1,995. | `[S23,S24,S26]` | USER-REPORTED [medium] |
| 2026-10-11 | Current state: `synthesise.ai` shows $2,999 one-time; ToS still says "monthly subscription". | `[S01,S03]` | VERIFIED [high] |

**Unknowns / flags.**
- The legal entity behind the **$97/month Whop storefront** named "Synthesise AI" is **not identified** and does not match the Dubai entity. `[UNKNOWN][medium][S16,S17]`
- Whether Synthesise AI was **built in-house or white-labelled/purchased** is **UNKNOWN**. One practitioner asserted on video that "Iman has not created these products, he's just purchased them", inferring this from the Microsoft/Monetise/Educate ecosystem logos on the site — that is a **user inference, not evidence**. `[USER-REPORTED][low][S22]`
- IAG Services FZCO's registration number/UBO, funding rounds and Microsoft Founders Hub participation are **not independently verifiable** from public sources found. `[UNKNOWN][low][S11]`

---

## C. Pricing history (with contradictions resolved)

### C.1 Synthesise AI itself

| Date (as observed) | Stated price / model | Source | Notes |
|---|---|---|---|
| 2025-04-27 | "JOIN THE WAITLIST" (no price) | Wayback S37 | Pre-launch |
| **2025-05-13** | **$1,499 / year** (lower tier) and **$2,999 / year** (tier labelled "Unlimited Credits") | Wayback S38 | Two annual tiers, both still waitlist-gated |
| 2025-04-28 | Directory listing reports "Starter Plan: $1,499/year (3 credits)… Higher Plan: from $2,999/year… Currently in **Closed Beta**" | S29 | Corroborates the 2-tier annual model |
| 2025-10 / 2026-07 | Waitlist only (no pricing exposed) | S39 | |
| **2026-10-11 (live)** | **"Starting $2,999 / one-time payment"** — Unlimited Credits, Personalised Dashboard, UVZ Calculation, Product Charter Generation, Premium Support + resources. CTA → `whop.com/checkout/plan_wPgM4iy64m7Js` | S01, S02 | Single tier now |
| **2026-10-11 (live ToS)** | "Synthesise AI operates on a **monthly subscription basis**… Refunds… within the first **seven (7) days** of the initial subscription. No refunds… for subsequent billing periods. To cancel… at least **48 hours before** the next billing cycle." | S03 | **Conflicts with the one-time price** |
| **2026-10-11 (Monetise offer page)** | Synthesise AI **"Value: $2,999"**, "Unlimited credits for 12 months" | S11 | Value stack, not a separate price |
| **2026-10-11 (Educate/Monetise ToS §11)** | "Access to GhostwriterOS, Synthesise AI, AskIman… is for **twelve (12) months** from the date of purchase… subject to **fair use**. Excessive or abusive use may result in suspension **without refund**." | S12, S13 | **Conflicts with "Unlimited Credits"** |
| Unverified third-party docs | "Starter Tier – $1,499/year (3 Creation Credits)", "Pro Tier – $2,999/year (Unlimited Build Credits)" | S08 | GitBook, likely unofficial |

### C.2 Contradictions and how they resolve

1. **"$2,999 one-time" vs "monthly subscription".** The marketing page and the ToS on the *same domain* disagree. Most likely explanation: the ToS is a **templated/copy-pasted legal document** (it still contains an unresolved placeholder in the Privacy Policy: *"\[confirm hosting locations, e.g. the United States and the European Union\]"*, S04) that was not updated when the commercial model changed. **Resolution: treat the published commercial price as $2,999 one-time; treat the refund/cancellation mechanics as governed by the Monetise 7-day refund (S12) rather than the ToS's "initial subscription" language.** `[CONTRADICTED][high][S01,S03,S04,S12]`
2. **"Unlimited Credits" vs "12 months, subject to fair use".** Not necessarily contradictory — the "unlimited" refers to within the 12-month window; the ToS adds a fair-use cap. **Resolution: unlimited ≠ perpetual; access is 12 months.** `[VERIFIED][high][S01,S11,S12]`
3. **$1,499 vs $2,999 vs $1,995.** Three different numbers across three different objects: (a) $1,499 was a **lower Synthesise tier in 2025**; (b) $2,999 is the **stated value / current Synthesise price**; (c) $1,995 is the **Monetise bundle price** that includes Synthesise. **No genuine contradiction** once objects are separated. `[VERIFIED][high][S11,S24,S38]`
4. **$97/month Whop storefront.** A fourth number with no vendor confirmation and a different legal footprint. `[UNKNOWN / possible impersonation][medium][S16,S17,S18]`
5. **Refund terms (Synthesise vs Monetise).** Vendor ToS for Synthesise says 7-day refund on the initial subscription; Educate/Monetise ToS §6 lists "Monetise – 7-day full refund guarantee. No questions asked." Both point to a **7-day window**; there is **no money-back guarantee** beyond it ("10x guarantee" and "Perfect Product Insurance" return *coaching and support*, not cash). `[VERIFIED][high][S03,S12,S25]`

---

## D. Complete observable user journey (account creation → exported product)

Reconstructed from two independent screen-recorded walkthroughs (Oct 2025), the Sept-2026 live-demo description, and the client-bundle route table. Timings are the reviewers' observations.

1. **Account creation / access.** User must have a Monetise account. `app.synthesise.ai` redirects `/` → `/signin`; the only auth path is **"Sign in using Whop"** (OAuth2). Support is `support@educate.io`. `[VERIFIED][high][S05]` *(In practice, users reach the tool through the Monetise Whop workspace, where it is embedded via Whop's Website-Embed app. `[VERIFIED][medium][S15]`)*
2. **Product intake.** Click **Create**. Specify the product/idea (e.g. "YouTube summarizer"), and a **language** (walkthrough showed English/French/Hindi). A flow is created (`flow.createFlow`). `[USER-REPORTED][high][S20,S43]`
3. **UVZ entry — two doors.** (a) "**Help me discover my UVZ**" (software researches the market), or (b) "**I have my own UVZ**" (user types their niche/problem). `[USER-REPORTED][high][S19,S20,S23]`
4. **Ingestion (undisclosed to the user).** Server-side tRPC procedures ingest **Instagram** content and **YouTube** videos (`flow.ingestInstagram`, `flow.ingestYoutube`, `flow.listYoutubeVideos`) and can parse an existing **GhostwriterOS audit** (`flow.parseGWOSAudit`). `[VERIFIED via client bundle][high][S43]`
5. **UVZ / "profitable pockets" generation.** 1.5–10 minutes observed (one run: ~1.5 min; others: 5–10 min). Produces **~10–20 micro-niche statements** (e.g. *"Help undergrads reduce exam anxiety by turning 2–3 hour lecture videos into 15-minute actionable Notion notes"*), each **scored out of 10**. `[USER-REPORTED][high][S19,S20,S22,S23]`
6. **Opportunity selection & validation.** User selects one UVZ → **Continue**. Scoring dimensions were observed as **pain**, **worsening**, **purchasing power**, **speed** (see §F). `[USER-REPORTED][medium-high][S20,S23]`
7. **Creator-audience mode (Sept 2026 only).** Paste an **Instagram handle or YouTube link**; the tool reads the back catalogue, comments and engagement and returns a profile (niche, transformation, content themes ranked by strength, audience, voice), then **4 scored product concepts**. `[USER-REPORTED][medium][S23]`
8. **Format selection.** Choose **e-book / video course / coaching offer**; further formats (digital asset, lead magnet, presentation) are locked behind "create a product to unlock"; video-course and coaching-offer paths required a **Monetise Pro** upgrade in the Oct-2025 build. `[USER-REPORTED][medium][S19,S23]`
9. **Step 1 — Visceral Transformation Map (VTM).** Maps "key transformations your customers will experience"; defines **before/after states**. Returns ~20–30 named transformation options (e.g. *"21-day promotion-ready watch plan"*, *"7-day RSVP machine challenge"*) each carrying a **suggested price point** (reviewers saw $149, $127–$147 suggestions). User picks one. **Not editable at this stage** in the Oct-2025 build. `[USER-REPORTED][high][S19,S20,S22]`
10. **Step 1b — Customer-journey / transformation analysis.** The chosen packaging + before/after states are converted into a **4-stage journey**: (1) awareness & triage, (2) method setup & first wins, (3) routine & trust, (4) scale & mastery; each stage gets concrete milestones. `[USER-REPORTED][medium-high][S19,S20]`
11. **Step 2 — Supporting references / knowledge base.** Upload a file **or** scrape a URL to build a "reference library" that "powers everything you build in this flow" (`resources.upload`, `resources.addYoutubeLinks`). Reviewer's note: they preferred pasting pre-scraped text files over URL scraping. `[USER-REPORTED][medium-high][S19,S22,S43]`
12. **Step 3 — Structure / summary.** Generates an **e-book summary**: overview, target audience, key objectives, plus a plan header (e.g. *"22,000 words, 100 minutes reading time, 9 chapters, 4 bonus materials"*). **This is the first fully editable screen** ("Edit details" — title, pricing, transformation promise, chapter list, bonuses). `[USER-REPORTED][high][S19,S22]`
13. **Outline → chapter/lesson planning.** Explicit TOC generated; then per-chapter content. Chapters map onto transformation stages (see §G). `[USER-REPORTED][high][S19]`
14. **Content generation.** Wave-based generation (multiple chapters in parallel-ish waves, "maintains context continuity while processing multiple sections"). Slow (reviewers: 2–10+ minutes for a ~20k-word e-book; up to 152 pages in one successful run). `[USER-REPORTED][medium-high][S19,S22]`
15. **Worksheets / templates / exercises / checklists.** The finished artefact includes a **checklist** and worksheets appended to the e-book (observed: "Hidden fees", "Vendor timeline and wedding-day packing and setup worksheet"). `[USER-REPORTED][medium][S22,S23]`
16. **Cover art.** AI-generated cover; **"Regenerate cover"** available; reviewers report **spelling mistakes** and poor aesthetics; no custom-upload path documented (one reviewer wished for Canva). `[USER-REPORTED][high][S19,S22]`
17. **Content refinement / editing.** A **section editor** exists with H1/H2/H3, bullets, code-block and table conversion, checklist insertion — but **no images or other media**, and reviewers call it "not a fully fleshed-out WYSIWYG". Manual section tweaking is required. `[USER-REPORTED][high][S19,S21]`
18. **Export.** **Download PDF**, with default margins or custom margins (reviewers noted custom margins can clip images). No EPUB/DOCX/Notion export was observed. `[USER-REPORTED][medium-high][S19,S21]`
19. **Product charter + hand-off.** The flow ends by generating a **product charter** ("the DNA of your offer" — offer, target audience, previous attempts, deliverables, marketing angle, positioning, scientific/method foundations, USP, objection handling), which the user is told to take to **Ghostwriter OS** for sales copy. `[USER-REPORTED][medium-high][S19,S20]`
20. **Publishing (Sept-2026 demo).** A **Whop store page** is generated from the same screen; the user sets a price and clicks **Publish** (demo: $150). `[USER-REPORTED][medium][S23]`
21. **Project history / iteration.** A **dashboard** lists flows and their status; flows can be reopened, and `flow.listYoutubeVideos`/trash routes (`/trash`, `/flow/@all|@shared|@deleted`) imply project management. **Progress tracking beyond a status list was not observed.** `[USER-REPORTED][medium][S20,S43]`

**Explicitly NOT observed anywhere:** multi-user collaboration, version history/diffing, A/B comparison of generated variants, comment/feedback loops, export to formats other than PDF, automated image insertion, or a human-review step. `[UNKNOWN][medium][S19,S21,S43]`

---

## E. Feature catalog

| Feature | Purpose | User inputs | Intermediate outputs | Resulting output + format | Editable / regenerable | Automated or human | Evidence source | Classification | Confidence | Unknowns |
|---|---|---|---|---|---|---|---|---|---|---|
| Flow creation (`flow.createFlow`) | Instantiate a project | Product name, language | Flow record | Project container (web) | Yes (reopen) | Automated | S20, S43 | VERIFIED | high | Max flows/credits semantics |
| UVZ discovery (market mode) | Find monetisable niches | Optional skill/problem text, or "not sure where to start" | Ranked "profitable pockets", scored /10 | ~10–20 niche statements (on-screen) | Selection only; input not editable post-run | Automated | S19, S20, S23 | USER-REPORTED | high | Whether external market data is queried |
| UVZ discovery (own mode) | Encode user's niche | Free-text niche/problem | Same scoring output | Niche statement | Input editable before run | Human input + automated scoring | S20 | USER-REPORTED | high | — |
| Creator-audience mode | Build a product *for* a creator's audience | Instagram handle or YouTube link | Creator profile (niche, transformation, themes, audience, voice) + 4 scored concepts | Concept list | Not observed | Automated (ingestion) | S23, S43 | USER-REPORTED | medium | Data source (API vs scrape), legal basis |
| UVZ scoring model | Rank opportunities | — | 4 dimensions: pain, worsening, purchasing power, speed (each /10) | Total score | No | Automated | S20, S23 | USER-REPORTED | medium-high | Whether demand/saturation/competition are scored separately |
| Visceral Transformation Map | Define before/after states | Selection of a transformation option | ~20–30 named options w/ suggested prices | Before/after + status text | **Not editable** at this step | Automated | S19, S20, S22 | USER-REPORTED | high | — |
| Customer-journey map | Stage the transformation | — | 4 stages w/ milestones | Journey map (screen) | Not observed | Automated | S19, S20 | USER-REPORTED | medium | — |
| Existing-solutions / competition analysis | Position vs alternatives | — | (claimed) analysis of existing solutions | Text in charter | — | Automated | S34, S35 | OFFICIAL CLAIM | low | No independent observation of a competition UI |
| Knowledge base / references | Ground generation in user content | File upload, URL scrape, YouTube links | Reference library | RAG context | Add/remove | Human supplies, automated ingest | S19, S22, S43 | USER-REPORTED | high | Chunking/retrieval internals UNKNOWN |
| Format picker | Choose deliverable type | E-book / video course / coaching offer (3 more locked) | Product skeleton | Route to sub-flow | — | Automated | S19, S23, S43 | USER-REPORTED | medium | Gate rules / plan tier mapping |
| E-book summary | Set scope | Editable title, pricing, promise | Overview, audience, objectives, word count, chapters, bonuses | Metadata | **Yes (edit details)** | Human-edited | S19, S22 | USER-REPORTED | high | Whether word count is enforced |
| Chapter generation | Produce manuscript | Approved outline | Wave-based chapter text | Chapter text | Yes (section editor) | Automated | S19, S22 | USER-REPORTED | high | Model provider UNKNOWN |
| Section editor | Light reformatting | Text edits | — | Edited manuscript | H1–H3, bullets, code, table, checklist; **no media** | Human | S19, S21 | USER-REPORTED | high | — |
| Cover generation | Produce cover art | — | Image prompt | Cover image (embedded in PDF) | "Regenerate cover" only | Automated | S19, S22 | USER-REPORTED | high | Upload path UNKNOWN; spelling errors observed |
| Worksheets / checklists | Add deliverables | — | — | Appended pages in the PDF | Partially editable | Automated | S22, S23 | USER-REPORTED | medium | — |
| Product charter | Blueprint for offer + marketing | — | Offer, audience, deliverables, positioning, USP, objections | Charter (screen) | Not observed | Automated | S19, S20 | USER-REPORTED | medium-high | Export format |
| Export | Deliver artefact | Margin settings | PDF render | **PDF** | Regenerate | Automated | S19, S21 | USER-REPORTED | high | EPUB/DOCX/Notion not found |
| Whop store publish | Sell the artefact | Price, store copy | Store page | Whop storefront | Republish changes | Automated + human price | S23 | USER-REPORTED | medium | Fees, refunds handled by Whop |
| Video-course builder | Course outline/scripts | Same as e-book | Modules, phases, scripts | Text (scripts/outline) | Regenerate | Automated | S20, S21 | USER-REPORTED | medium-high | **No slides, no video, no avatars** |
| Presentations sub-flow (`/presentations`, `/presentation/:id/present(/notes)`) | Slide-style deliverable | — | Outline/notes | Presentation (in-app) | — | Automated | S43 | VERIFIED (route exists) | medium | Not demonstrated publicly |
| Dashboard / history | Track projects | — | Flow list, status | Dashboard | — | Automated | S20, S43 | USER-REPORTED | medium | Analytics depth UNKNOWN |
| Admin/dev surfaces (`/admin/*`, `/dev/*`) | Internal ops | — | — | Internal-only | — | — | S43 | VERIFIED (routes exist) | medium | Not user-facing |

---

## F. UVZ deep-dive

**What the product appears to collect.**
- Free-text skill/niche/problem statement **or** the "not sure where to start" market-scan option. `[USER-REPORTED][high][S19,S20,S23]`
- A target creator's **Instagram handle / YouTube link** (Creator-audience mode). `[USER-REPORTED][medium][S23]`
- A **knowledge base** of uploaded files / scraped URLs / YouTube links. `[USER-REPORTED][high][S19,S22,S43]`
- Language preference; product name; later, pricing and positioning fields. `[USER-REPORTED][high][S20]`
- The client bundle also references ingestion of **Instagram** and **YouTube** content and of a **GhostwriterOS audit** as first-class flow inputs. `[VERIFIED via client bundle][high][S43]`

**What it appears to compute.**
- A set of candidate "Unique Value Zones" / "profitable pockets" — natural-language niche statements. `[USER-REPORTED][high][S19,S20,S23]`
- A **score out of 10 per candidate**, decomposed into four named dimensions observed on screen: **pain**, **worsening**, **purchasing power**, **speed**. `[USER-REPORTED][medium-high][S20,S23]`

**Is there a scoring/ranking system?** Yes — a **visible 4-dimension, 0–10 summed score** with candidates ranked. `[USER-REPORTED][medium-high][S20,S23]` It is the single most copy-worthy mechanic in the product. `[INFERENCE][medium]`

**Are demand, expertise, saturation and willingness-to-pay evaluated separately?**
- **Willingness-to-pay: partially — as "purchasing power".** `[USER-REPORTED][medium][S20]`
- **Demand: implied by "pain"/"worsening" + (claimed) market scan.** Not a separately named demand metric. `[INFERENCE][medium][S19,S23]`
- **Expertise: handled implicitly at input time** ("I have my own UVZ") rather than scored. `[INFERENCE][medium][S20]`
- **Saturation / competition: NOT observed as a scored dimension.** It appears only as marketing copy ("identify gaps in digital product markets") and in a claimed separate "existing solutions" step. `[UNKNOWN][medium][S23,S27,S34]`

**Is market data retrieved externally?** Yes — the run emits status text such as *"processing seven web search results"*, *"configuring multi-angle market analysis"*, *"cross-correlating marketing data points"*, *"intelligence caching market-intelligence database"*. `[USER-REPORTED][medium][S19]` **Which provider, and whether results are cached, is UNKNOWN.** The "$3 billion in Whop sales data" claim is not corroborated in any technical surface. `[OFFICIAL CLAIM][low][S11]`

**Are sources cited?** Vendor claims **"45 pages synthesised from over 150 sources"** and that "the AI hands you the reference list". In the live demo the **source list was never displayed**, and no walkthrough exposed a citation UI. `[CONTRADICTED (claim unshown)][low-medium][S23]`

**Can users compare/reject alternatives?** Partially: a list of ~10–20 candidates is shown, and users pick one. But **comparison tooling is absent** (no side-by-side, no filters/deltas), **the original UVZ input cannot be edited after generation**, and restarting requires deleting the product and starting a new flow. `[USER-REPORTED][high][S19,S20]`

**Is any ML component verifiable?** **No.** The client bundle exposes no model-provider string, no embedding/vector endpoint, and no scoring model. "Machine learning", "cosine similarity" and "vectorized corpus" appear **only in marketing copy and in the unverified GitBook**. `[UNKNOWN][medium][S01,S07,S43]`

**Bottom line for a developer:** the reproducible, valuable mechanic is **the rubric + the pipeline**, not any proprietary model. A 4-factor 0–10 opportunity score, candidate list, and a forced single-choice selection is cheap to build and is the part users credit. Everything beyond that (demand data, saturation, citations, "ML") is **unsubstantiated**. `[INFERENCE][medium]`

---

## G. Customer transformation & product architecture

**How a broad problem becomes an outcome-promising product.**
The chain is: **raw expertise/problem → UVZ statement → scored selection → before/after states → 4-stage journey → e-book "packaging" with a named promise → chapter plan → manuscript → worksheets/checklist → PDF → charter → (Ghostwriter OS) sales page → (Whop) storefront.** `[USER-REPORTED][medium-high][S19,S20,S22,S23]`

**How chapters/deliverables map to transformation stages.**
Observed pattern: the **4-stage journey** (awareness & triage → method setup & first wins → routine & trust → scale & mastery) is mapped to a **per-stage deliverable** and then to chapters. Example from the walkthrough: stage milestones ("audit the backlog", "create timestamped highlights", "complete the full week of required trainings", "maintain a zero-training backlog for 30 days") became chapters *"1 The case for watch-what-matters learning", "2 Your AI toolbox setup", "3 Timestamping mastery", "4 10-minute actionable playlist method", "6 Prompt playbook"*, plus bonus deliverables (*"done-for-you prompt pack", "12 Notion board templates", "eye-friendly workspace checklist", "ROI calculator"*). `[USER-REPORTED][medium-high][S19]`

**Structural note that matters.** The e-book is assembled as **sections**; the UI treats the manuscript as a set of **named sections with per-section status** (pending/complete) rather than a single document — which is why the flow is resumable but also why incomplete sections silently hang. `[USER-REPORTED][medium][S19,S22]`

**Claimed (unverified) framing:** the tool is pitched as mapping "the customer journey" and "existing solutions" as discrete steps before the transformation map. The 6-step list appears in two landing-page variants: *"① Discover Your Unique Value Zone ② Understand the Customer Journey ③ Identify Existing Solutions ④ Develop Your 5-Stage Transformation Map ⑤ Create Your Digital Product Outline ⑥ Develop Tools & Worksheets."* `[OFFICIAL CLAIM][medium][S34,S35]` — note "5-Stage Transformation Map" in the 1.0-era copy vs the **4 stages** actually observed in 2025–26 builds. `[CONTRADICTED (version drift)][medium][S19,S34]`

---

## H. Output artifact catalog

| Output | Visible structure | Depends on | Format |
|---|---|---|---|
| UVZ / profitable-pocket list | ~10–20 statements, each scored /10; 4 sub-scores in creator mode | Input text or market scan; ingestion | On-screen list |
| Creator profile (creator mode) | Niche, transformation, ranked content themes, audience, voice | IG handle / YT link | On-screen |
| Visceral Transformation Map | ~20–30 named transformation options, each with a suggested price; then before/after + status text | Selected UVZ | On-screen |
| Customer journey | 4 stages, each with milestones | VTM selections | On-screen |
| E-book summary | Overview, audience, objectives, word count, reading time, chapter count, bonus materials | Journey + references | Editable metadata |
| Outline / TOC | Chapter list mapped to stages | Summary | On-screen |
| Manuscript | Per-section text, wave-generated, only bold titles as formatting | Outline + KB | Text in editor |
| Worksheets / checklists | Appended pages | Manifest source | Inside PDF |
| Cover image | Single generated image | Content title | Embedded image |
| **E-book** | 9–10 sections + checklist + worksheets | All of the above | **PDF download** (margin options) |
| Product charter | Offer, audience, prior attempts, deliverables, marketing angle, positioning, method foundations, USP, objection handling | Manuscript | On-screen (used by Ghostwriter OS) |
| Video-course artefacts | Phases + modules + scripts/outlines | Same flow | Text only (no slides, no video) |
| Presentation artefacts | Presentation + presenter notes | Same flow | In-app (`/presentation/:id/present/notes`) |
| Whop store page | Store copy + price + publish button | Finished product | Whop storefront |

**Do steps share one project context or are they independent generations?**
They **share one project context keyed by `flow_id`**: the routes are nested (`/flow/:flow_id/unique-value-zone`, `/flow/:id/digital-product/:pid/{product-charter,structure,customize,resources,export,package,vtm}`, `/flow/:id/digital-product/:pid/ebook-{editor,package,references,structure}`). `[VERIFIED via client bundle][high][S43]` Practical consequence: **downstream artefacts inherit upstream choices**, and the "vtm" artefact is generated **per product artefact** (e.g. `ebook` vs `digital-asset`), not once per flow — i.e. the transformation map is re-computed for each packaging. `[VERIFIED via route table][high][S43]`

---

## I. Customer experience themes

**Independent reports actually reviewed: 4 hands-on video reviews (S19, S20, S21, S22) + 1 dated third-party review site with member access (S23–S26) + 2 review-site writeups (S27, S28) + 1 aggregated-sentiment summary (S30). Affiliate-bias note: S23–S28 and S30 all earn commission on Monetise/summit signups, and S22 promotes its own free community — so positive and negative writeups alike carry incentive, in opposite directions.** `[VERIFIED for the bias statement][high][S23,S24,S28,S30]`

| Theme | What the independent sources actually report | Sources |
|---|---|---|
| **Ease of use** | "Click click click"; almost nothing to type in market-scan mode; step-by-step is signposted. But «"even I keep losing track of where the next steps are"» and the format gate was not discoverable. | S22; S19 |
| **Time-to-product** | 30 min → 152 pages (best case); 45–60 min for one e-book including manual pass; 2 hours for a *better* Claude-made alternative. One run took ~10 min per generation wave. | S22, S21 |
| **Content depth** | "Content is decent, not great"; "structure and planning is extremely good"; one reviewer rated the human-made Claude alternative "at least four times better". | S19, S21 |
| **Genericness** | Described as "Iman's custom version of ChatGPT with pre-built prompts and templates"; "80–100% of this can be done inside Claude". | S21, S19 |
| **Market-research accuracy** | Reviewer's own niche test produced mostly irrelevant niches; he judged that supplying your **problem** rather than your product produced better UVZ output. | S19 |
| **Manual editing burden** | "You will have to manually tweak sections significantly, design the PDF, enhance formatting, polish everything for hours." Formatting "abysmal"; no images in the e-book. | S21, S19, S22 |
| **Export/delivery** | PDF download works; margins can clip images; no other export format found. | S19, S21 |
| **Bugs** | Cannot edit UVZ input after generation; must delete and re-create; chapter generations get stuck (refresh required); cover spelling errors; occasional UI elements invisible/hard to click; "a little bit buggy". | S19, S22 |
| **Price-to-value** | Independent estimate: "maybe $100–$200 a year" / "$200–$300 a year" standalone; "not worth $3,000"; "don't buy just for this software". | S19, S21 |
| **Refunds** | 7-day window only; "10x guarantee" ≠ refund; "you cannot really test the method in 7 days". Aggregated testimonials include refund complaints ("They refused to give me a refund because it was over 7 days after purchase"). | S25, S30 |
| **Aggregate sentiment signal** | Whop listing ~4.9★ over thousands of **in-product** reviews vs an **unclaimed Trustpilot** profile at 2.9★ over 3 reviews; the review site's own read: "solid production, oversold AI". | S26, S30 |

**Net:** the CX evidence is **polarised by use-case**. Reviewers who treat it as a **lead-magnet factory / ideation engine** are positive (S22, S23). Reviewers who treat it as a **finished-product factory** are negative (S19, S21). Both are describing the same software. `[INFERENCE][high]`

---

## J. Differentiation vs "strong general LLM + good prompts + templates"

**Evidenced differentiators (things a general LLM setup does not give you out of the box):**

1. **An enforced, opinionated pipeline with state.** The `flow/:id/...` route graph and per-artefact `vtm` are a **durable multi-stage state machine**; chat gives you none of that. `[VERIFIED via route table][high][S43]`
2. **Ingestion of *someone else's* audience.** Creator-audience mode ingests an Instagram/YouTube back catalogue plus comments/engagement to derive audience + themes, then scores product concepts for that audience. This is a genuinely different workflow to "paste your own expertise into a chat". `[USER-REPORTED][medium][S23,S43]`
3. **A visible, comparable opportunity score.** Four named dimensions scored /10 gives the user a decision artefact. Chat gives prose. `[USER-REPORTED][medium-high][S20,S23]`
4. **Knowledge-base grounding per project** (uploaded files / scraped URLs / YouTube links) rather than ephemeral chat context. `[USER-REPORTED][high][S19,S22,S43]`
5. **One-click packaging + distribution** into a Whop store page with a price. `[USER-REPORTED][medium][S23]`
6. **Non-technical default UX** — near-zero prompt authorship required; the sequence is the product. `[USER-REPORTED][high][S19,S22]`

**Hypotheses (asserted by vendor, not evidenced):** proprietary training on "$3B Whop sales data"; "150+ sources with citations"; "machine learning" UVZ; "reduces months of work".

**Evidenced counter-differentiators (things that erase the moat):**

- Output **formatting quality is worse** than a well-prompted Claude run that also emits a formatted PDF (one reviewer produced a TOC'd, copyright-paged PDF with Claude + Python). `[USER-REPORTED][medium-high][S21]`
- **80–100% replication** is the explicit verdict of two independent practitioners. `[USER-REPORTED][medium][S19,S21]`
- The pipeline is **batch-oriented and slow** (minutes per wave) with no visible model choice or prompt control. `[USER-REPORTED][high][S19]`

**Verdict:** the differentiated value is **the rubric + ingestion + pipeline state + distribution hook** — copyable mechanics — not the generative engine. `[INFERENCE][high][S19,S21,S22,S23,S43]`

---

## K. Unresolved questions + searches that returned nothing

### Unresolved questions
1. What LLM/provider(s) power generation? **No provider string in the client.** `[UNKNOWN][medium][S43]`
2. Is the 4-dimension score computed by an LLM, a heuristic, or a mix? `[UNKNOWN][medium][S20,S43]`
3. Which external market-data provider supplies the "web search results"/"market intelligence database"? `[UNKNOWN][medium][S19]`
4. What is the actual credit/usage accounting behind "unlimited… subject to fair use"? No published definition. `[UNKNOWN][medium][S01,S12]`
5. Are there really 150+ cited sources, and is a reference list exposed anywhere in-app? Not shown. `[UNKNOWN][low-medium][S23]`
6. Is the $97/month "Synthesise AI" Whop storefront authorised, a reseller, or an impersonation? `[UNKNOWN][medium][S16,S17,S18]`
7. Are the GitBook docs first-party? No link from any vendor page; content contradicts the product. `[UNKNOWN→likely no][medium-high][S06–S10]`
8. Does any export format other than PDF exist (EPUB/DOCX/Notion/Whop-native)? Not found. `[UNKNOWN][medium][S19,S21]`
9. Is there any real usage/outcome data (average products shipped, refund rates, retention after the 12-month software window)? Vendor publishes none; `monetise.com/disclaimer` returns 404. `[UNKNOWN][medium][S26]`
10. GDPR/UAE PDPL posture: the Privacy Policy ships an unedited placeholder for hosting locations. `[CONTRADICTED/defect][high][S04]`

### Searches run that returned nothing useful (or only dead ends)
- `"Synthesise AI" review trustpilot OR scam OR refund` → no Trustpilot page for synthesise.ai itself; returned Monetise-level pages only. **No first-party Trustpilot profile exists for Synthesise AI.** `[S?]`
- `site:reddit.com synthesize.ai OR synthesise.ai` → no dedicated Synthesise AI thread found; only Iman-Gadzhi/Monetise-level threads. Reddit direct fetches all blocked (403). `[S30 aggregator only]`
- `"synthesise.ai" help centre OR "knowledge base" OR support OR FAQ official` → **no official help centre exists**; results were Synthesia's (`help.synthesia.io`) and unrelated products. Support is a bare `support@educate.io` mailbox. `[S05,S12]`
- `"Synthesise AI" "export to notion"` → no relevant result (only the walkthrough's mis-transcribed status line "export to notion"). `[S19]`
- `"Synthesise AI" free trial` → **no free trial exists**; no trial/plan page found. `[S24]`
- `"Synthesise AI 4.0"` → **no independent reporting on a 4.0 release**; the explainer explicitly says details are sparse and to treat version hype sceptically. `[S40]`
- `Synthesise AI` + `Educate.io` support/Help Centre → only ToS/policy pages (`educate.io/policy/terms`), no product docs. `[S13]`
- `synthesise.ai` sitemap → contains **only `/` and `/404`**; no docs, changelog or help routes. `[S01 sitemap]`
- `freesummitaccess.online/synthesize-ai/` (a review the search engine ranks highly for this query set) → **HTTP 404, page removed**. `[S42]`
- `scamadviser.com/check-website/app.synthesise.ai` → **HTTP 403**, could not be opened. `[S?]`
- YouTube transcript extraction for `vmxh9ok8gCM`, `_bfnnPIwMPE`, `kQZc9MttMh4`, `ccOSF-mZhgg` **succeeded** via yt-dlp auto-captions. The two originally-suggested videos (`vmxh9ok8gCM`, `_bfnnPIwMPE`) did **not** expose transcripts through the built-in reader (yt-dlp install failed there) — resolved by installing yt-dlp locally. Where a video had no captions this is stated. `[S19–S22]`

### Search list (distinct queries executed, ≥15 satisfied)
1. `"Synthesise AI" Iman Gadzhi` · 2. `"synthesise.ai"` · 3. `Synthesise AI Iman Gadzhi reddit` · 4. `"Synthesise AI" "Unique Value Zone"` · 5. `"Ghostwriter OS" "Synthesise AI" reddit review` · 6. `"Synthesise AI" review trustpilot OR scam OR refund` · 7. `"Synthesise AI" "creator profile" OR "Monetise Pro" OR "video course builder"` · 8. `"Synthesise AI" free trial OR login OR pricing 2026` · 9. `"Synthesise AI" vs ChatGPT digital product generator` · 10. `Iman Gadzhi "AskIman" AI Monetise 2026` · 11. `site:reddit.com synthesize.ai OR synthesise.ai` · 12. `"IAG Services FZCO" Synthesise OR Educate OR Gadzhi` · 13. `"Synthesise AI" "profitable pockets" OR "unique value zone" score` · 14. `"Synthesise AI" knowledge base OR "export to notion" OR "product charter"` · 15. `"Synthesise AI" 2026 update OR bugs OR "not worth" Monetise software` · 16. `Synthesise AI demo tutorial ebook generation Iman Gadzhi youtube` · 17. `"synthesise.ai" help centre OR "knowledge base" OR support OR FAQ official` · 18. `"Synthesise" AI Iman Gadzhi "profitable pocket" OR "creator profile" walkthrough 2026` · 19. `"Synthesise AI" "3 questions" / whop standalone` (via whopscope discovery).

---

## Appendix 1 — Mechanism notes for the reader's own e-book factory (copy-worthy vs skip)

**Worth copying (low cost, evidenced user benefit)**
- **A visible multi-factor opportunity score with named dimensions and a 0–10 scale.** Cheap to implement; the strongest single differentiator users cite. `[S20,S23]`
- **Forced candidate list + single selection + persisted choice.** Creates a decision record the rest of the pipeline can reference. `[S19,S43]`
- **One flow ID threaded through every artefact, with a per-artefact transformation map.** Enables resume, reuse and cross-artefact consistency. `[S43]`
- **Ingestion of a *third party's* content (creator handle/URL) to generate audience-derived concepts.** A genuinely novel workflow that general chat workflows don't replicate. `[S23,S43]`
- **Per-project knowledge base with file upload + URL scrape + YouTube link ingest.** `[S19,S22]`
- **Terminal "publishing artefact" (store copy + price + publish).** Reduces time-to-revenue friction. `[S23]`
- **Editable metadata step (title/pricing/promise/chapter list) *before* long generation.** Users repeatedly praised this as the point where they regained control. `[S19,S22]`

**Skip / fix (evidenced failure modes)**
- **Uneditable intermediate stages** (UVZ input, VTM before/after). Users hit this immediately and it forces full restarts. `[S19]`
- **"Unlimited" claims with hidden fair-use caps in the ToS.** Legal/pricing inconsistency that only damages trust. `[S01,S12]`
- **Cover-art generation as the only image path.** Users want upload/Canva. `[S19,S22]`
- **Plain-text export with only bold titles.** The single biggest quality complaint. Emit real structured documents (headings, TOC, lists, tables, images, page breaks). `[S19,S21]`
- **Sources claimed but not shown.** If you retrieve sources, *show them and cite them inline*. `[S23]`
- **Batch "wave" generation with no progress/telemetry.** Generations hang silently; expose per-section status and retry. `[S19,S22]`

---

## Appendix 2 — Disambiguation (for search hygiene)

Confusable products that pollute results for this query set: **Synthesia** (AI video avatars, `synthesia.io`), **Synthesis AI** (synthetic training-data for computer vision), **Synthesised.ai** (`synthesised.ai`, an AI-consulting content site), **Synthesize.com / adsynthesize.ai**, and **Synthesise AI (this product)**. Also *not* the same: the `$97/month` Whop storefront "Synthesise AI" (Montréal). `[VERIFIED][medium-high][S40,S16]`
