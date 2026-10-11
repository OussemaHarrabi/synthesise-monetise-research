# Lead researcher — first-hand primary evidence

All observations made **2026-10-11** by the lead researcher, using direct HTTP
requests (`curl`) and page fetches. These are the highest-grade artefacts in the
dossier: they are reproducible by anyone and do not depend on a third party's
summary of the product.

---

## E1. Marketing site is a Framer template site — VERIFIED (high)

```
$ curl -sS -D - -o /dev/null https://synthesise.ai/
HTTP/1.1 200 OK
Etag: "1509db542ecc6d439456c6d1c00cf881"
Framer-Site-Id: 0abe48b376e59b62b18cceafd19791041d6ebb838d958b6e6cc1d50773bdf7b2
Last-Modified: Wed, 09 Sep 2026 19:45:42 GMT
Link: <https://framerusercontent.com>; rel="preconnect"
Server: Framer/26fa766
Server-Timing: region;desc="eu-west-3", cache;desc="cached", ssg-status;desc="optimized"
Strict-Transport-Security: max-age=31536000
```

`synthesise.ai` is served by **Framer**, not by application infrastructure.

### E1a. The Framer layers retain a *personal-finance* template's names — VERIFIED (high)

Extracted from the live page HTML (`data-framer-name` attributes, unmodified):

* `data-framer-name="Centralizing all your monthly expenses in one place to control your cash flow and excel in financial planning."`
* `data-framer-name="Amplify your financial freedom with smart and secure payment solutions."`
* `data-framer-name="Upcoming payments"` and `data-framer-name="Track and manage your upcoming payments with our insightful overview."`
* `data-framer-name="$29.99 / month"` (used as the layer name of the pricing block that actually renders `$2,999`)

One of those leftover strings is *visible* to users on the live site: the
`Faster Product Development` section contains the orphan sentence
**"Instant insights, personalized control – your financial dashboard powerhouse."**

**Interpretation (INFERENCE, high):** the marketing site is a licensed/re-used
Framer *finance* template whose copy was partially overwritten. This is not
evidence about the application; it is evidence about how the *claims* were
produced, and it materially lowers the credibility of the marketing copy.

---

## E2. Application is Next.js on Vercel — VERIFIED (high)

```
$ curl -sS -D - -o /dev/null https://app.synthesise.ai/
HTTP/1.1 307 Temporary Redirect
Location: /signin
Server: Vercel
X-Powered-By: Next.js
Vary: rsc, next-router-state-tree, next-router-prefetch, next-router-segment-prefetch
X-Matched-Path: /
X-Vercel-Cache: MISS
X-Vercel-Id: cdg1::iad1::mkklq-1791743906691-3045474ee8b3
Link: </_next/static/immutable/media/Nohemi_Regular-s.p.1uhh2jg7fsfuf.ttf>; rel=preload
Strict-Transport-Security: max-age=63072000; includeSubDomains; preload
```

* `X-Powered-By: Next.js`, `Server: Vercel`, App-Router RSC headers (`Vary: rsc, next-router-state-tree, …`).
* App-router asset scheme `/_next/static/immutable/…`.
* Self-hosted fonts: **Nohemi** (display) and **SF Pro** (text).
* `stricty`-style security headers present (`X-Frame-Options: SAMEORIGIN`, nosniff).

**Consequence:** the public "Foundational Technology Stack" claim (Rust core,
Kubernetes, WASM, Redis) in the Synthesise GitBook is **not corroborated by any
observable surface** and is directly contradicted for the customer-facing tier.

---

## E3. Checkout is Whop, not a first-party payment stack — VERIFIED (high)

```
$ curl -sS -D - -o /dev/null https://whop.com/checkout/plan_wPgM4iy64m7Js
HTTP/1.1 200 OK
Server: cloudflare
set-cookie: __cf_bm=…; Domain=whop.com
```

Every "GET STARTED NOW" CTA on `synthesise.ai` points at
`https://whop.com/checkout/plan_wPgM4iy64m7Js`. Payment, checkout, and (per the
product's own model) delivery ride on **Whop**, a third-party marketplace.

The GitBook's claim of a first-party *"Stripe webhook processor for payments,
renewals, plan upgrades"* is therefore **UNVERIFIED** (Whop may itself settle via
Stripe, but that is not evidence of Synthesise's own billing service).

---

## E4. Legal entity and jurisdiction — VERIFIED (high)

`synthesise.ai/policies/terms` §1:

> This website and the Synthesise AI application … are owned and operated by
> **IAG Services FZCO**, doing business as "Synthesise AI".

§13: governed by the **federal laws of the United Arab Emirates** as applied in
the **Emirate of Dubai**; disputes by binding arbitration under the **Dubai
International Arbitration Centre (DIAC)** rules; seat Dubai; one arbitrator;
English.

`synthesise.ai/policies/privacy-policy` §1 and §2 repeat **IAG Services FZCO**,
`support@synthesise.ai`, and cite the **UAE Personal Data Protection Law
(Federal Decree-Law No. 45 of 2021)** plus EU/UK GDPR.

### E4a. The privacy policy shipped with an unresolved template placeholder — VERIFIED (high)

Privacy policy §9 (International Transfers), quoted verbatim:

> including by our hosting and AI providers located in **\[confirm hosting
> locations, e.g. the United States and the European Union\]**.

A bracketed editorial instruction left in a live legal document. Together with
E1/E1a this establishes that the public copy is assembled from templates with
incomplete substitution.

### E4b. Registry footprint — VERIFIED (medium; third-party registry aggregator)

`info-clipper.com` company profile for **IAG SERVICES - FZCO**:

* Address: `DSO-IFZA-20424, IFZA Properties, Dubai Silicon Oasis`
* City: Dubai, United Arab Emirates
* Telephone (partially masked): `+971 58…`
* DUNS (partially masked): `84…`

The **same address block** is printed in the footer of `educate.io/policy/terms`:
`IAG SERVICES - FZCO, DSO-IFZA-20424, IFZA Properties, Dubai Silicon Oasis Dubai
United Arab Emirates`. Two independent sites, one registered address ⇒ the same
operator.

---

## E5. The ecosystem is one legal entity with many brands — VERIFIED (high)

`join.digital-launchpad.com/policies/terms` §opening:

> This website (the "Site") is owned and operated by **IAG SERVICES FZCO**,
> doing business as **Educate.io**.

`educate.io/policy/terms` §opening: identical wording, same entity.

### E5a. Software bundle clause — VERIFIED (high)

Both terms documents, §11 **Use of Software Products**:

> Access to **GhostwriterOS, Synthesise AI, AskIman, and Alchemy of Self** is for
> **twelve (12) months** from the date of purchase (unless otherwise stated at
> checkout) and is subject to fair use.

⇒ Synthesise AI is **one SKU inside an education bundle**, distributed by the same
entity that sells the education programmes — not a standalone SaaS with its own
user base and subscription economics.

### E5b. The programme catalogue and refund windows — VERIFIED (high)

Educate.io terms §6 lists, with product-specific refund rules:

| Product | Refund rule as published |
|---|---|
| Monetise | 7-day full refund |
| Monetise Pro | 7-day full refund |
| Agency Accelerator | 7-day full refund |
| Digital Launchpad | first payment only, 72 hours |
| Command Centre | 7-day full refund |
| Boardroom | refunds available until **2025-06-17** |
| Mindset Accelerator | refunds **2025-10-07 → 2025-10-14** |
| MMOC 2.0 VIP Ticket | refunds **2025-10-07 → 2025-10-14** |
| AI Income Workshop VIP Ticket | refunds until **2026-01-25** |
| Alchemy Of Self | refunds until **2026-01-25** |

The dated windows are a **launch calendar**: Boardroom ≤ Jun 2025, Mindset
Accelerator / MMOC 2.0 in Oct 2025, AI Income Workshop / Alchemy of Self in
Jan 2026. Each launch carries its own refund deadline.

### E5c. Programme guarantees — VERIFIED (high)

* **Monetise Guarantee** — "continue to provide coaching and support until you
  have generated revenue equal to at least **ten (10) times** the amount of your
  initial purchase price, for a period of up to **three (3) years**"
  (worked example: $2,000 → $20,000). Explicitly *"not a money-back guarantee or
  promise of specific earnings"*.
* **Monetise Pro Guarantee** — if you do not generate **≥ $50,000** within the
  initial **90-day** coaching period, Pro coaching is renewed for a further 90
  days at no extra cost.

⇒ The guarantee is a **retention mechanism paid in coaching hours**, not a
financial guarantee. Any "guaranteed results" framing in affiliate content is a
misreading of this clause.

### E5d. Iman Gadzhi link — VERIFIED (high)

Educate.io terms §15 (DMCA): *"email **info@iman-gadzhi.com** with a proper DMCA
notice"*. The DMCA contact for the entity that owns Educate.io and Synthesise AI
is an `@iman-gadzhi.com` address.

---

## E6. Synthesise AI pricing history — VERIFIED (high; Wayback + live)

Live checkout page (`synthesise.ai`, 2026-10-11):

> **Starting $2,999 / one-time payment** — Unlimited Credits · Personalised
> Dashboard · Unique Value Zone Calculation · Product Charter Generation ·
> Premium Support + resources

Wayback Machine, `https://synthesise.ai/`, snapshot **2025-05-13**
(`web.archive.org/web/20250513162511id_/https://synthesise.ai/`) — **two tiers**:

| Tier | Rendered price (2025-05-13) | Included |
|---|---|---|
| Lite | **$1,499** | **3 Credits**, Unique Value Zone, Product Charter |
| Pro | **$2,999** | **Unlimited Credits**, Unique Value Zone, Product Charter |

Wayback availability for `synthesise.ai`: `20240519` (302), `20241213` (200),
`20250109` (200), `20250302` (200), `20250427` (200), `20250513` (200),
`20251006` (200), `20260718` (200). The domain was live as a site from at least
**2024-12-13**; the "3 credits / unlimited credits" tiering is attested only in
the May 2025 snapshot among those captured.

**Reported third-party price points** (classification USER-REPORTED, medium):
a YouTube review titled *"REVIEW of @ImanGadzhi Synthesise AI — Is it Worth
$1500?"* is consistent with the $1,499 tier.

### E6a. Two documents from the same entity disagree about the payment model — CONTRADICTED (high)

| Source (all IAG Services FZCO) | Stated model |
|---|---|
| `synthesise.ai` live pricing block, 2026-10-11 | **"$2,999 / one-time payment"** |
| `synthesise.ai/policies/terms` §9, live 2026-10-11 | **"operates on a monthly subscription basis"**, billed at the start of each cycle; refund within the **first seven (7) days** of the *initial subscription*; cancel ≥48 h before renewal |
| `educate.io/policy/terms` §11, 2026-08-10 | software access is for **twelve (12) months** from purchase |
| `join.digital-launchpad.com/policies/terms` §11 | identical 12-month clause |

**Resolution attempt:** the three statements cannot all be literally true for one
SKU sold on one checkout. The most economical explanation (INFERENCE, medium-high)
is that the Synthesise ToS is **inherited template text** (it is a near-verbatim
sibling of the Educate.io/Digital Launchpad terms, which are also reused across
every brand), and that the *operative* offer is a one-time payment granting
12 months of access, with the "monthly subscription / 7-day refund" clause
carried over unedited. The evidence for template inheritance is strong
(E1/E1a legal placeholder, shared clause structure, shared DIAC/Dubai clauses,
shared §11 wording).

**Practical reading for a buyer or competitor:** assume *one-time payment, 12
months of access, refund governed by whatever the checkout page says* and treat
every other published statement as unreliable until it is confirmed at the point
of sale.

---

## E7. The "official documentation" is almost certainly not the product — CONTRADICTED (high)

`synthesise-ai.gitbook.io/synthesise-ai` (`Introduction`, `Key Features`,
`Architecture`, `AI Agents`, `Tokenomics`, `Holder Benefits`) presents itself as
Synthesise AI's documentation. It is **not linked from `synthesise.ai`** (whose
only footer links are Privacy Policy and Terms & Conditions).

### E7a. What it claims

* Stack: "**Rust** for core service logic and agent execution", "**WASM** … on
  the edge", "**PostgreSQL + Redis**", "**OpenAI, Anthropic, and Local LLMs**",
  "**Kubernetes** for horizontal scaling".
* Named agents: **TutorAgent, SEOAgent, UXAuditAgent, MonetizationAgent**;
  elsewhere **SEOAgent, LaunchCopyAgent, InstructionalDesigner**.
* UVZ engine: *"cosine similarity across a vectorized corpus of monetizable
  problems"*; validation: *"prompt multiplexing, GPT-ensemble confidence
  scoring, competitive saturation indexing"*.
* A **$SYNAI token** launching on **pump.fun (Solana)**, SPL, with
  **95 % liquidity pool / 5 % dev reserve**, credit purchase, staking revenue
  share paid in USDC, and NFT minting.

### E7b. Why it is not credible as documentation — VERIFIED (high)

1. **Every code sample is a decorative stub.** The "sample Rust interface that
   powers our chatbot sandboxing layer" is a `HashMap` that stores one string and
   returns `format!("AI ({}): processing '{}'", …)`. The "agent trait
   implementation" for `SEOAgent` returns a hardcoded literal
   `format!("SEO tags for '{}': future, disruption, AI", input)`. The
   "cosine_similarity" snippet is the only piece of real code present.
2. **It contradicts every observable fact** (E1–E3): the frontend is Next.js on
   Vercel and the marketing site is Framer. There is no observable Rust, WASM,
   Kubernetes, Redis or first-party Stripe surface.
3. **It adds a token sale to a $2,999 education product**, with a
   95 %-liquidity/5 %-dev allocation on `pump.fun` — a structure associated with
   speculative launches, not with a company that already charges $2,999.
4. It is hosted on a self-serve GitBook with no verifiable authorship, and its
   opening "Platform Backing & Ecosystem" text (Microsoft Founders Hub /
   Monetise / Educate) is **the same sentence as the official landing page**,
   i.e. copied from it.

**Classification:** the *stack*, *agent names*, *UVZ algorithm* and *token* claims
are **OFFICIAL CLAIM (unattributed)** at best and **UNVERIFIED / likely
AI-generated** in fact. Confidence that this is *not* an authoritative technical
document: **high**. Whether it is an unauthorised brand-affiliated site, an
abandoned/aspirational official doc, or a token promotion riding the brand is
**UNKNOWN** — but it must not be used as evidence about the real implementation,
and the brand/impersonation risk it represents is a real commercial finding.

### E7c. What *is* usable from it — the product-surface vocabulary

The GitBook's *feature* names reproduce the marketing vocabulary of the real
product and are useful for search: "Unique Value Zone (UVZ) Engine", "Concept
Validation Protocol", "Product Charter Generator" (with "Deliverable Tree
Engine", "Persona & Journey Map", "Pricing Engine"), "Smart Assistant Dashboard"
("Creation History + Modular Resume", "Visual Feedback Analytics"), "Chatbot
Engine", "Visual Automation Engine". Treat as **OFFICIAL CLAIM (marketing
vocabulary)**, not as implemented systems.

---

## E8. Business Together is a live webinar funnel, not a company — USER-REPORTED / OFFICIAL CLAIM (medium)

Search results for "Business Together" against the Iman Gadzhi brand return
`Let's Start a $373/Day Business Together (LIVE)` — a recurring **live training
webinar**, with replay pages that expire ("Replay Expires May 4th", "Replay
Expires Oct 6") and third-party summaries. A Reddit thread exists asking
`Is Iman Gadzhi's $373/Day Business Together Legit?`.

**Reading (INFERENCE, medium):** "Business Together" is the *top-of-funnel live
event* used to launch/renew the paid programmes (Monetise, Monetise Pro,
Digital Launchpad, …) — i.e. a marketing mechanism of the same entity, not a
separate company. The naming ("Together") and the per-launch refund deadlines
(E5b) are consistent with a continuously re-run launch calendar.

*Limitation:* the Reddit thread body could not be retrieved — reddit.com serves a
JS challenge to non-browser clients and the fetch returned only the challenge
page. Classified accordingly; no content is quoted from it.

---

## E9. Search-surface facts worth recording

* `monetise.com/waitlist` exists (indexed) ⇒ Monetise has a dedicated domain and
  a waitlist funnel.
* `askiman.ai` — "Your 24/7 AI Business Mentor" ⇒ a second AI product in the
  bundle, with its own domain.
* `growyouragency.com/terms` — a third IAG-family terms page.
* Review-site price points for Monetise conflict: **$999** and **$1,995** are
  both reported (`sidehustlesummitreviews.com/monetise-pricing/` is titled
  "Monetise Cost: Why You See Both $999 and $1,995"), and one review site asserts
  "Side Hustle Summit vs Monetise: They're the Same $1,995 Offer". This is the
  same pattern as E6a — **a single offer presented under multiple front-end
  brands at different prices**, which is a launch/funnel artefact, not a
  contradiction in the code.

---

## E10. The shipped client bundle exposes the real feature map — VERIFIED (high)

Method (legitimate: these are publicly served static assets the app advertises to
any unauthenticated visitor):

```bash
curl -sS https://app.synthesise.ai/signin -o signin.html
grep -oE '"/_next/static/immutable/chunks/[^"]+\.js"' signin.html | tr -d '"' | sort -u > chunks.txt
# fetch each chunk (30 chunks, ~2.0 MB total), then scan the concatenation
```

### E10a. Library fingerprint — VERIFIED (high)

Present as literal strings in the served chunks:

| Evidence | Means |
|---|---|
| `"/api/trpc"` (×3), `https://github.com/trpc/trpc/issues/new` | **tRPC** client |
| `"/api/auth"` | **NextAuth.js / Auth.js** route convention |
| `blitz-js/superjson` issue link | **superjson** transformer (tRPC defaults) |
| `sentry.browser.*`, `sentry.client.config.ts`, `sentry-trace`, `sentry-tracing-init` | **Sentry** browser SDK, including tracing |
| `react.dev/errors/`, `react.memo_cache_sentinel` | React (RSC build) |
| `core-js 3.38.1` licence URL | `core-js` polyfill |
| `radix.slottable`, `DialogOverlay`, `dismissableLayer`, `focusScope` | **Radix UI** primitives (shadcn-style component layer) |
| `WebAssembly.Exception`, `Symbol.asyncDispose` | modern runtime features only |

No model-provider identifier (`openai`, `anthropic`, `claude`, `gpt-*`, `gemini`,
`mistral`, …) appears anywhere in the client bundle. **Model choice is
server-side and is not publicly observable.**

### E10b. Complete client route table — VERIFIED (high)

74 distinct routes were recoverable from the bundle. They are the product's real
feature inventory (grouped by the researcher):

**Project container**
`/flow/:flow_id` · `/flow/@all` · `/flow/@shared` · `/flow/@deleted` · `/trash`
`/share/:token` · `/create` · `/dashboard` · `/dashboard/settings` · `/support` ·
`/learn` · `/trending` · `/landing` · `/design-system`

**Discovery / definition stages**
`/flow/:flow_id/unique-value-zone` · `/flow/:flow_id/creator-outline` ·
`/flow/:flow_id/supporting-resources` · `/flow/:flow_id/lead-magnet`

**Digital product (the main line)**
`…/digital-product/:product_id`
`…/product-charter` · `…/structure` · `…/customize` · `…/resources` ·
`…/export` · `…/package` · `…/vtm` ·
`…/:phase_id/:module_id` · `…/:phase_id/:module_id/slides-outline`

**E-book sub-pipeline (explicitly separate from the generic product)**
`…/ebook-editor` · `…/ebook-package` · `…/ebook-references` · `…/ebook-structure`

**Course / coaching / live-delivery sub-pipelines**
`…/course-references` ·
`…/coaching-mentorship` · `…/coaching-module/:module_id` ·
`…/coaching-references` · `…/coaching-usp`

**Presentations (separate top-level artefact type)**
`/presentations` · `/presentations/outline/:id` · `/presentation/:id` ·
`/presentation/:id/present` · `/presentation/:id/present/notes` ·
`/presentation/:id/slides-outline`

**Digital assets (bundleable, with their own VTM)**
`…/digital-asset/:digital_asset_id` · `/bundle` · `/vtm`

**Commerce / delivery**
`/store` · `/flow/:flow_id/store` ·
`/whop-app/storefront/:storeId` ·
`/whop-app/storefront/:storeId/product/:storeProductId`

**Admin**
`/admin` · `/admin/overview` · `/admin/users` · `/admin/users-management` ·
`/admin/models` · `/admin/ai` · `/admin/products` · `/admin/stores` ·
`/admin/clicks` · `/admin/clicks/:slug/analytics` · `/admin/bug-bot-test`

**Internal "dev" tool surfaces shipped in the same table**
`/dev/brain` · `/dev/ebook` · `/dev/image-loader` · `/dev/jev` ·
`/dev/lead-magnet` · `/dev/storefront` · `/dev/storefront/:storeId` ·
`/dev/storefront/:storeId/product/:storeProductId` · `/dev/uvz`

**Reading:** `vtm` occurs twice as a per-artefact stage — most plausibly
**"Visual/Virtual Transformation Map"**, i.e. the transformation map the user
asked about is a first-class, persisted stage attached to both digital products
and digital assets. There is a **separate e-book pipeline with its own structure,
references, editor and packaging steps** — directly comparable to the user's
factory — and separate course/coaching pipelines and a presentation/present-mode
pipeline.

### E10c. Verified tRPC procedures (partial) — VERIFIED (high)

```
flow.createFlow          flow.ingestInstagram     flow.ingestYoutube
flow.listYoutubeVideos   flow.parseGWOSAudit      product.createForFlow
resources.upload         resources.addYoutubeLinks
```

**Two conclusions that materially change the earlier guesswork:**

1. **The "skills/expertise discovery" step is ingestion-based.** The product
   authenticates the creator and *ingests their Instagram and YouTube content*
   into the flow — UVZ input is the creator's real published material, not a
   questionnaire alone.
2. **There is a verified cross-product integration**: `flow.parseGWOSAudit`
   parses a **GhostwriterOS** ("GWOS") audit as an input to a Synthesise flow.
   GhostwriterOS is the other AI product in the same bundle (E5a). So the
   "ecosystem" is not just co-marketing: at least one product consumes another's
   artefact.

> **CORRECTION (2026-10-11, later the same day).** An earlier version of this
> file listed `store.buyer.*` as a verified procedure prefix. That was an
> inference from an error-classification map inside the bundle and it is **not
> correct**: the delegated technical audit probed the live tRPC endpoint and
> `store.buyer.get` / `store.buyer.me` return `-32004 NOT_FOUND` (unknown path),
> whereas registered-but-protected procedures return `-32001 UNAUTHORIZED` and
> registered mutations return `-32005` on GET. The verified procedure set is in
> `synthesise_tech_and_prompts.md` §A.4/§B: `flow.*`, `product.{createForFlow,get}`,
> `charter.get`, `course.get`, `resources.{upload,addYoutubeLinks,get}`,
> `files.{generateSasUrl,get}`, `presentation.{get,outline.get}`,
> `webSearch.{unsplashSearch,search}`, `log.clientError`, `admin.users.list`.
> Verified **absent**: `uvz.*`, `billing.*`, `credits.*`, `export.*`,
> `analytics.*`, `competitor.*`, `opportunity.*`, `persona.*`,
> `admin.models.*`, `admin.ai.get`. Authentication is **Better Auth** (not
> NextAuth), and the API layer is **tRPC v11 + superjson + Zod**. See that file
> for the full evidence; it supersedes my E10a library inference on auth.

**UNKNOWN (still):** the definition of `vtm`, the server-side prompt/model for
UVZ or charter generation, whether "agents" exist as separate processes (the
route table shows *stages of one pipeline*, not a roster of autonomous agents),
and anything behind `/admin`.

---

## Method note

Commands used (reproducible):

```bash
curl -sS -D - -o /dev/null https://synthesise.ai/
curl -sS -D - -o /dev/null https://app.synthesise.ai/
curl -sS -D - -o /dev/null https://whop.com/checkout/plan_wPgM4iy64m7Js
curl -sS "http://web.archive.org/cdx/search/cdx?url=synthesise.ai&output=text&fl=timestamp,original,statuscode,digest&collapse=digest&limit=200"
curl -sS "https://web.archive.org/web/20250513162511id_/https://synthesise.ai/"
```

Page fetches: `synthesise.ai/`, `synthesise.ai/policies/terms`,
`synthesise.ai/policies/privacy-policy`, `educate.io/policy/terms`,
`join.digital-launchpad.com/policies/terms`,
`synthesise-ai.gitbook.io/synthesise-ai/{llms.txt,getting-started/introduction,getting-started/key-features,platform-infrastructure/architecture,core-modules/ai-agents,usdsynai-information/tokenomics}`,
`info-clipper.com` company record.

No authentication was bypassed. No paid or private surface was accessed. The
only "app" surface touched was the public sign-in redirect and the publicly
served static JS/CSS it advertises.
