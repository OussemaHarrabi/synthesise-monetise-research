# Synthesise AI — Technology Stack, Prompt/Agent Evidence, and Reverse-Engineering Dossier

- **Compiled:** 2026-10-11 (UTC). **Access date for every source:** 2026-10-11.
- **Subject domain:** `synthesise.ai` (marketing) and `app.synthesise.ai` (application).
- **Production deployment observed:** `data-dpl-id="dpl_4U29zfpU2gSrZBJQtA6KJewkTvZt"`, Next.js build as served on 2026-10-11.
- **Method:** direct HTTP inspection (`curl` headers/bodies, `read` on URLs), static analysis of **41 JavaScript chunks** served by the live app, a **tRPC procedure-existence oracle** (see §A.4), DNS resolution via Google DoH, and 25+ distinct web/GitHub/registry searches. **No authentication was used and no account was created**; nothing behind the login wall was accessed.
- **Classification vocabulary used:** `VERIFIED` (directly observed, reproducible) · `OFFICIAL CLAIM` (operator/marketing assertion) · `USER-REPORTED` (third-party assertion) · `INFERENCE` · `UNKNOWN` · `CONTRADICTED`.
- **Hygiene note required by the brief:** a secondary "tech stack" page or a plausible-looking code sample is **not** proof. Where that is all that exists, it is explicitly marked.

---

## KEY FINDINGS

1. Two separate systems under one brand: the marketing site `synthesise.ai` is a **Framer**-built static site (`Server: Framer/26fa766`, `Framer-Site-Id: 0abe48b3…`, generator meta `Framer`), while the product `app.synthesise.ai` is a **Next.js App Router** application. [VERIFIED][high][T01,T04]
2. `app.synthesise.ai` is hosted on **Vercel**: `Server: Vercel`, `X-Vercel-Cache`, `X-Vercel-Id`, and `app.synthesise.ai` CNAMEs to `cc2d0c2eafe29416.vercel-dns-016.com`. [VERIFIED][high][T21]
3. The app is **Next.js ≥ 16.3** with production **Turbopack**: assets are served from `/_next/static/immutable/chunks/…`, a path form that Next.js documents as the immutable-static-assets feature added in **v16.3.0**; `Vary: rsc, next-router-state-tree, next-router-prefetch, next-router-segment-prefetch` confirms App Router/RSC; `X-Powered-By: Next.js`. [VERIFIED][high][T04,T23]
4. API layer is **tRPC v11** exposed at `/api/trpc`, with **superjson** as the transformer (superjson's own deserialisation error string is present in the client bundle). Error codes returned live are tRPC's (`-32001` UNAUTHORIZED, `-32004` NOT_FOUND, `-32005` METHOD_NOT_SUPPORTED). [VERIFIED][high][T05,T06]
5. Authentication is **Better Auth**, not NextAuth. Direct evidence: the client bundle contains Better Auth's own internals (`Symbol.for("better-auth:broadcast-channel")`, `Symbol.for("better-auth:focus-manager")`, `Symbol.for("better-auth:online-manager")`, `"better-auth.message"`), its `get-session` fetch and `/api/auth` default `basePath`, and `plugins[].fetchPlugins`; the server exposes `X-Matched-Path: /api/auth/[...all]`, `/api/auth/ok → {"ok":true}` and `/api/auth/get-session → null`. [VERIFIED][high][T04,T05,T07,T08] — **this corrects a sibling agent's interim "NextAuth" inference**, which rested only on the generic `/api/auth` prefix and the `signIn.oauth2()` client call (Better Auth deliberately mirrors next-auth's client API, so that call is non-discriminating).
6. Identity/payments/entitlement come from **Whop**: the sign-in screen offers "Sign in using Whop" and a client call `signIn.oauth2({providerId:"whop", callbackURL:…})`; checkout is `https://whop.com/checkout/plan_wPgM4iy64m7Js`. [VERIFIED][high][T04,T01,T19]
7. Product creation is modelled as a **"flow"** with an ingested creator-audience source: verified live procedures include `flow.createFlow`, `flow.ingestInstagram`, `flow.ingestYoutube`, `flow.listYoutubeVideos`, `flow.parseGWOSAudit`. [VERIFIED][high][T06]
8. A **`charter` tRPC router exists and is auth-gated** (`charter.get` → `-32001 Authentication required`), as do `product.get`, `course.get`, `files.get`, `resources.get`, `presentation.get`, `presentation.outline.get`, `admin.users.list`. [VERIFIED][high][T06]
9. There is **no `uvz.*`, `billing.*`, `credits.*`, `export.*`, `analytics.*`, `competitor.*`, `opportunity.*` or `persona.*` procedure** at the observed namespace depth — the "Unique Value Zone", pricing and competition analysis are therefore not separately addressable services with those names. [VERIFIED][high][T06]
10. File uploads are brokered through a **SAS-URL generator** (`files.generateSasUrl` → `-32005` on GET = registered mutation). "SAS" (Shared Access Signature) is Azure Blob terminology, and the official site claims Microsoft Founders Hub membership — so Azure Blob is the **likely** store. [INFERENCE][medium][T06,T01]
11. Image sourcing inside the product uses the **Unsplash API** for slide/block imagery (`webSearch.unsplashSearch`, plus a `webSearch.search` mutation), with a debounced client search UI. [VERIFIED][high][T05,T06]
12. A public **presentation/slide generator** exists as a first-class subsystem: `presentation.slides.create|regenerate|duplicate|group|reorder|bulkDelete|toggleSkipped`, `presentation.blocks.modify|delete|retryImage`, `presentation.share.comments.*`, with block-level regeneration accepting an **optional user prompt** ("Optionally describe what you want, or leave blank to regenerate from the slide's context."). [VERIFIED][high][T05]
13. Client-side observability/analytics stack = **Sentry** (`sentry.browser.*`, a live `ingest.us.sentry.io` DSN with org `4509693378625536`, `sentry-trace` metadata), **Microsoft Clarity** (a `ClarityAnalytics` RSC component), **Segment**, **Amplitude**, and **nprogress**. [VERIFIED][high][T05]
14. UI layer = **Tailwind CSS + shadcn/ui-style primitives over Radix** (`data-slot`/`data-variant`/`data-size` attributes, `ring-offset-background`/`bg-muted`/`text-foreground` design tokens), **hugeicons** + **lucide**, **sonner** (toasts), **framer-motion**, and **@dnd-kit**-style drag/drop internals. [VERIFIED][high][T05]
15. Fonts shipped by the app: **DM Sans, Nohemi, Inter, Geist, SF Pro, Fraunces, Schibsted Grotesk**; there is also a `/design-system` route. [VERIFIED][high][T04]
16. **No LLM provider, model name, or model version token appears anywhere in the client bundles.** Searches for `openai|anthropic|claude|gpt-*|gemini|mistral|llama|grok|deepseek|perplexity|openrouter|bedrock|vertex` returned zero client-side hits. Model choice is a **server-side secret**. [VERIFIED][high][T05]
17. **No prompt/agent evidence is public.** No leaked system prompt, agent list, agent count, or prompt-injection report for Synthesise AI exists in any searchable surface (GitHub code search is login-gated; GitHub issues, Reddit, X, blogs, npm, PyPI, Hugging Face all returned nothing product-specific). The only public prompts in the ecosystem belong to an unrelated third-party reimplementation (§C.1) and must not be attributed to Synthesise AI. [VERIFIED absence][high][T24,T34,T36,T37,T38]
18. **`synthesise-ai.gitbook.io` is NOT the operator's documentation.** It claims a Rust/WASM/PostgreSQL+Redis/Kubernetes core, first-party Stripe webhooks and four named agents — all presented with decorative stub code samples — and it also documents a **Solana pump.fun token `$SYNAI`** (95 % "liquidity pool" allocation). Every technological claim in it is contradicted by the observable deployment (Next.js/Vercel × no Rust artefacts, and Whop — not Stripe — for billing). Treat as **UNVERIFIED / almost certainly AI-generated or scam-adjacent**. [CONTRADICTED][high][T13,T14,T15,T16,T17]
19. Exactly **one** third-party project in the entire surveyed space targets these products: `Master-Urus/Shadow-Operator`, self-described as *"an alternative to Ghostwriter OS and Synthesize OS, created by iman gadzhi monetize.com"*. It is a **concept-level inspired reimplementation**, not a clone and not a reverse-engineering report — it is offline-first (Next.js + Python scraper + **Ollama `qwen3:4b`**) and its prompts use Hormozi's Value Equation and Cialdini/Walker frameworks, with **no** reference to UVZ, charter, or any Synthesise internals. 1 star, 2 commits. [VERIFIED][high][T26,T27,T28,T29]
20. A **named commercial competitor**, `monetiseos.com` (MonetiseOS), markets itself explicitly with the meta-keywords *"synthesise AI alternative, ghostwriter OS alternative"* and ships a 5-step pipeline (Creator Monetisation Analysis → Full Digital Product → Marketing Funnel → 14-Day Launch Plan → Creator Pitch) sourced from an Instagram/YouTube URL. It is a **generic competitor** running on Vercel with a *different* backend shape (no `/api/trpc`, no Better Auth session endpoint). [VERIFIED][high][T30]
21. Pricing evidence is **internally contradictory** across three operator-controlled surfaces observed on 2026-10-11: the Framer marketing site's search index says **"$2,999 / one-time payment"**, the Whop storefront sells **"$121.25 → $97 / month"**, and third-party reviews quote **$1,500** and confirm the separate **$1,995** Monetise programme that bundles a 12-month Synthesise licence. [CONTRADICTED][medium][T18,T19,T20,T32,T33]
22. The operator is **IAG Services FZCO** (Dubai, UAE), trading as Synthesise AI, governed by UAE/Dubai law with DIAC arbitration, on a **monthly subscription with a 7-day refund window**. The published Privacy Policy still contains an unfilled editor placeholder — `[confirm hosting locations, e.g. the United States and the European Union]` — indicating boilerplate legal text. [VERIFIED][high][T11,T12]
23. Support addresses are inconsistent: the in-app sign-in screen links `support@educate.io` while the published policies use `support@synthesise.ai`. [VERIFIED][high][T04,T11,T12]

---

## A. Stack claim-by-claim

### A.0 The two surfaces

| Component | Evidence observed 2026-10-11 | Source |
|---|---|---|
| Marketing site builder | Body comment `Made in Framer · framer.com`, `meta name="generator" content="Framer 0e6a859"`, all scripts from `framerusercontent.com`, `Server: Framer/26fa766`, `Framer-Site-Id`, `Vary: Framer-Navigation` | T01, T02 |
| Marketing site publish date | Framer publish comment/meta `Published Sep 9, 2026, 7:45 PM UTC`; policy pages carry `published: "Sep 9, 2026, 7:45 PM UTC"` | T01, T11, T12 |
| Marketing site route surface | `sitemap.xml` lists only `/` and `/404`; its `framer-search-index` JSON is publicly fetchable and contains the full on-page copy incl. pricing | T03, T18 |
| App surface | `X-Powered-By: Next.js`, `Server: Vercel`, `/_next/static/immutable/chunks/*.js` (Turbopack), RSC flight payload inlined as `self.__next_f.push(...)` | T04 |

### A.1 Frontend

| Claim | Evidence | Corroborating sources | Classification | Confidence |
|---|---|---|---|---|
| Next.js App Router (RSC) | `X-Powered-By: Next.js`; `Vary: rsc, next-router-state-tree, next-router-prefetch, next-router-segment-prefetch`; flight data `self.__next_f`; component tree with `parallelRouterKey`, `OutletBoundary`, `MetadataBoundary`, `next-size-adjust` meta | T04 | VERIFIED | high |
| Next.js **≥ 16.3** | Asset paths are `/_next/static/immutable/chunks/<hash>.js` (no `?dpl=` on immutable assets). Next.js docs: immutable static assets added in **v16.3.0** | T23 (+ official Next.js release-notes page) | VERIFIED (path form) → version is a strong INFERENCE | high |
| Turbopack production build | `/…/chunks/turbopack-40twjh2vcbrlg.js`; module IDs referenced as `I[530373,[…]]`; `(0,n.c)(28)` memo-cache idiom; `react.memo_cache_sentinel` throughout | T04, T05 | VERIFIED | high |
| Tailwind CSS | Utility class soup (`bg-background`, `text-muted-foreground`, `ring-offset-background`, `overscroll-none`, `min-h-dvh`); CSS emitted to `/_next/static/immutable/chunks/*.css` | T04 | VERIFIED | high |
| shadcn/ui-style + Radix primitives | `data-slot="button"`, `data-variant`, `data-size` attributes; Radix internals (`dismissableLayer`, `DialogOverlay`, `MenuPortal`, `forceMount`, `__scopeMenu`); `radix` token ×93 in chunk corpus | T04, T05 | VERIFIED | high |
| Iconography | `HugeiconsIcon` + `Loading01Icon` module exports (`hugeicons` ×34); `lucide` ×6 | T05 | VERIFIED | high |
| Toasts | `sonner` ×127 (Toaster mounted at top-right in the root layout) | T04, T05 | VERIFIED | high |
| Animation | `framer-motion` internals (`MotionCheckAppearSync`, `KeyframeResolver`, `valueSubscriptions`) | T05 | VERIFIED | high |
| Drag-and-drop | `droppableContainers`, `draggableNodes`, `collisionRect`, `measuringScheduled` → **@dnd-kit**-family internals | T05 | VERIFIED (library family) | medium-high |
| Fonts | CSS `@font-face` + `<link rel=preload>` for DM Sans, Nohemi (`.ttf`), Inter, Geist, SF Pro (`.ttf`), Fraunces, Schibsted Grotesk; hashed class names e.g. `dm_sans_a261e6ce-module__On3upq__variable` | T04 | VERIFIED | high |
| Design-system page exists | Route `/design-system` in the embedded route manifest | T04 | VERIFIED | high |
| tRPC client | `TRPCReactProvider` RSC component; `"/api/trpc"` literal; client calls `api.<router>.<proc>.useQuery/useMutation` | T04, T05 | VERIFIED | high |
| **Cloudinary** | `cloudinary` appears **only** inside Next.js's own `imageConfigDefault` loader enum (`["default","imgix","cloudinary","akamai","custom"]`) — i.e. framework boilerplate. A component named `AdvancedImage` exists with Cloudinary-like props (`showLoadingIndicator`) but is exported from a local webpack module and **no cloud name or Cloudinary config is present anywhere**. | T05 | **NOT EVIDENCED** | n/a |

### A.2 Backend / API / data

| Claim | Evidence | Classification | Confidence |
|---|---|---|---|
| **tRPC v11** at `/api/trpc` | Live error payload `{"code":-32004,"httpStatus":404,"path":"health","zodError":null}` for `/api/trpc/health`; `-32005 METHOD_NOT_SUPPORTED` for GET on registered mutations; `-32001 UNAUTHORIZED` for protected queries. `zodError` field in the error envelope is tRPC v11's schema-validation slot. | VERIFIED | high |
| **superjson** transformer | Bundle contains superjson's exact `Trying to deserialize unknown class '…' - check https://github.com/blitz-js/superjson/issues/116#issuecomment-773996564` and `customTransformerRegistry` | VERIFIED | high |
| **Zod** for input validation | `zod` token ×1119 in the client chunk corpus (v11 error envelope also exposes `zodError`) | VERIFIED | medium-high |
| **Better Auth** | See KEY FINDING 5. Bundle: `Symbol.for("better-auth:*")` ×3 namespaces, `better-auth.message` channel name, `get-session` GET fetch, `basePath` default `"/api/auth"`, `plugins.flatMap(p=>p.fetchPlugins)`, `lifecycle-hooks` plugin id. Server: `X-Matched-Path: /api/auth/[...all]`, `/api/auth/ok → {"ok":true}`, `/api/auth/get-session → null`, `/api/auth/session → 404` | VERIFIED | high |
| **Whop** OAuth + entitlement | `Sign in using Whop` button rendering a local `whop_logo_brandmark_orange.svg`; `signIn.oauth2({providerId:"whop",callbackURL})`; `WHOP_BUYER_REQUIRED_MESSAGE`; `/whop-app/storefront/:storeId` routes | VERIFIED | high |
| Email OTP sign-in path | Sign-in screen: "Enter the email you purchased Monetise with." + "Send Code" submit button (disabled until email entry) | VERIFIED | high |
| **Resend** (transactional email) | `resend` ×12 in the corpus (client-side references are weak; treated as indicative only) | INFERENCE | low |
| Relational DB / ORM / queue | **No ORM, driver, queue or job-runner identifier is recoverable client-side** (no Prisma/Drizzle placeholder text, no connection strings, no Supabase/Firebase/Upstash keys). Server-only. | **UNKNOWN** | n/a |
| Azure Blob storage | `files.generateSasUrl` procedure name (SAS = Azure Shared Access Signature) + official "Microsoft Founders Hub" claim. No blob hostname is observable. | INFERENCE | medium |
| Object-store upload flow | Client calls `api.files.generateSasUrl.useMutation()` then `api.files.get.useQuery({imagesOnly:true})` inside an image-picker dialog (max 6 files, "PNG, JPG or GIF") | VERIFIED | high |
| Background jobs / streaming | `text/event-stream` appears **once only**, inside Sentry's fetch instrumentation denylist (`nl=["text/event-stream","application/x-ndjson","application/stream+json"]`), i.e. framework code, not app code. The GitBook's "streaming response mode" is unverified. No app-level SSE/WebSocket endpoint was observed. | **UNKNOWN** | n/a |
| Server compute | Not observable. The only hosting fact is Vercel (`Server: Vercel`). | **UNKNOWN** | n/a |

### A.3 Analytics / observability / content

| Claim | Evidence | Classification | Confidence |
|---|---|---|---|
| **Sentry** | `sentry.browser.*` keys, `sentry.client.config.ts` reference, `sentry-trace`/`sentry-org_id` on observed pages, DSN host `o4509693378625536.ingest.us.sentry.io`, `https://docs.sentry.io` string, `beforeSendSpan` internals | VERIFIED | high |
| **Microsoft Clarity** | RSC module named `ClarityAnalytics` mounted in the root layout of `/signin` | VERIFIED | high |
| **Segment** | `segment` ×283 in the corpus | VERIFIED | medium-high |
| **Amplitude** | `amplitude` ×1 in the corpus | VERIFIED (presence) | medium |
| **nprogress** | Injected `<style>#nprogress .bar{background:#db2777…}` plus `NProgress` config (`color:"#db2777"`, `height:2`) | VERIFIED | high |
| **Vercel** hosting | `Server: Vercel`, `X-Vercel-Cache`, `X-Vercel-Id`, `Vercel-Vary`, CNAME → `…vercel-dns-016.com` | VERIFIED | high |
| Vercel Analytics / Speed Insights | **Not evidenced** on the pages fetched (no `/_vercel/insights/script.js`); only a `vercel.live` preview-toolbar reference (dev/preview artefact) | **UNKNOWN** | n/a |
| CMS | `contentful` ×7 in the corpus — no Contentful client config, space ID or delivery call is observable. **Weak/ambiguous.** | UNKNOWN (single-source) | low |
| Unsplash images | `api.webSearch.unsplashSearch` + `UnsplashSearch` React component consuming `e.urls.regular` and `e.user.name`; debounced 500 ms; offered as a "Unsplash" tab beside local upload | VERIFIED | high |
| Google Fonts | `preconnect` to `fonts.gstatic.com` + `@font-face` `src: url(https://fonts.gstatic.com/s/dmsans/v17/…)` | VERIFIED | high |

### A.4 The client-route manifest (strongest single artefact)

A JavaScript chunk served to anonymous visitors (`42wg8eh_d5753.js`, 247 KB, build `dpl_4U29zfpU2gSrZBJQtA6KJewkTvZt`) embeds the application's **complete client route table** as `{"path":…,"regex":…,"paramNames":…}` objects. Public routes:

```
/  /create  /dashboard  /dashboard/settings  /design-system  /landing  /learn
/not-authorized  /presentations  /products  /signin  /store  /support  /trash  /trending
/admin  /admin/ai  /admin/bug-bot-test  /admin/clicks  /admin/models  /admin/overview
/admin/products  /admin/stores  /admin/users  /admin/users-management
/dev/brain  /dev/ebook  /dev/image-loader  /dev/jev  /dev/lead-magnet  /dev/storefront  /dev/uvz
/flow/@all  /flow/@deleted  /flow/@shared
```

Parameterised routes:

```
/admin/clicks/:slug/analytics
/dev/storefront/:storeId
/dev/storefront/:storeId/product/:storeProductId
/flow/:flow_id
/flow/:flow_id/creator-outline
/flow/:flow_id/lead-magnet
/flow/:flow_id/store
/flow/:flow_id/supporting-resources
/flow/:flow_id/unique-value-zone
/flow/:flow_id/digital-product/:product_id
/flow/:flow_id/digital-product/:product_id/product-charter
/flow/:flow_id/digital-product/:product_id/structure
/flow/:flow_id/digital-product/:product_id/vtm
/flow/:flow_id/digital-product/:product_id/customize
/flow/:flow_id/digital-product/:product_id/package
/flow/:flow_id/digital-product/:product_id/export
/flow/:flow_id/digital-product/:product_id/resources
/flow/:flow_id/digital-product/:product_id/:phase_id/:module_id
/flow/:flow_id/digital-product/:product_id/:phase_id/:module_id/slides-outline
/flow/:flow_id/digital-product/:product_id/ebook-editor
/flow/:flow_id/digital-product/:product_id/ebook-package
/flow/:flow_id/digital-product/:product_id/ebook-references
/flow/:flow_id/digital-product/:product_id/ebook-structure
/flow/:flow_id/digital-product/:product_id/course-references
/flow/:flow_id/digital-product/:product_id/coaching-mentorship
/flow/:flow_id/digital-product/:product_id/coaching-module/:module_id
/flow/:flow_id/digital-product/:product_id/coaching-references
/flow/:flow_id/digital-product/:product_id/coaching-usp
/flow/:flow_id/digital-asset/:digital_asset_id
/flow/:flow_id/digital-asset/:digital_asset_id/bundle
/flow/:flow_id/digital-asset/:digital_asset_id/vtm
/presentation/:id  /presentation/:id/slides-outline
/presentation/:id/present  /presentation/:id/present/notes
/presentations/outline/:id
/share/:token
/whop-app/storefront/:storeId
/whop-app/storefront/:storeId/product/:storeProductId
```

**Technical inferences from this artefact (labelled):** the data model is `flow → {digital_product | digital_asset | presentation}`, with digital products further typed into **ebook / course / coaching** variants that each have their own reference-material screens; `vtm` appears for both products and assets; `/dev/*` routes indicate a live internal R&D surface shipped in production; `/whop-app/*` indicates an embedded Whop app surface. [INFERENCE][medium-high][T04]

### A.5 The tRPC procedure-existence oracle (reproducible)

Because tRPC answers a **registered mutation** reached via GET with `-32005 METHOD_NOT_SUPPORTED` and a **registered protected query** with `-32001 UNAUTHORIZED`, while answering an **unknown path** with `-32004 NOT_FOUND`, procedure existence can be verified without credentials. Probe URL form:

```
https://app.synthesise.ai/api/trpc/<router>.<proc>?batch=1&input=%7B%220%22%3A%7B%22json%22%3Anull%2C%22meta%22%3A%7B%22values%22%3A%5B%22undefined%22%5D%7D%7D%7D
```

**VERIFIED to exist (2026-10-11):**

| Namespace | Procedures verified | How verified |
|---|---|---|
| `flow` | `createFlow`, `ingestInstagram`, `ingestYoutube`, `listYoutubeVideos`, `parseGWOSAudit` | −32005 (registered mutations) |
| `product` | `createForFlow` (mutation), `get` (query, 401) | −32005 / −32001 |
| `charter` | `get` | −32001 |
| `course` | `get` | −32001 |
| `resources` | `upload`, `addYoutubeLinks` (mutations), `get` (query, 401) | −32005 / −32001 |
| `files` | `generateSasUrl` (mutation), `get` (query, 401) | −32005 / −32001 |
| `presentation` | `get`, `outline.get`, `getEverything`, `utils.getBaseUrl`, `share.get`, `share.comments.list` (queries); `deleteSlide`, `blocks.modify`, `blocks.delete`, `blocks.retryImage`, `slides.create`, `slides.regenerate`, `slides.duplicate`, `slides.group`, `slides.reorder`, `slides.bulkDelete`, `slides.toggleSkipped`, `share.comments.create` (mutations — from bundle literals) | −32001 / −32005 / bundle |
| `webSearch` | `unsplashSearch`, `search` (both mutations) | −32005 |
| `log` | `clientError` (mutation) | −32005 |
| `admin` | `users.list` (query) | −32001 |

**VERIFIED NOT to exist** (returned −32004 NOT_FOUND): `uvz.get|generate|list|create`; `charter.generate`; `flow.get|list|delete`; `product.getCharter|list|generate`; `products.get|list`; `presentations.get`; `slides.get`; `ebook.get`; `ebook.structure.get`; `coaching.get`; `asset.get`; `assets.get`; `files.list`; `resources.list`; `billing.get`; `credits.get|list`; `user.get`; `user.me`; `users.me`; `admin.models.list|get`; `admin.ai.get`; `store.get`; `store.buyer.me`; **`store.buyer.get`**; `webSearch.tavily`; `log.get`; `analytics.get`; `export.get|create`; `keyword.get`; `opportunity.get`; `competitor.get`; `audience.get`; `persona.get`; and control `notAReal.procedure`.

> **Correction to a sibling agent's notes:** the wildcard listing `store.buyer.*` does **not** contain `store.buyer.get` — that literal path is NOT_FOUND. `store.buyer.get` should not be cited as a real procedure.

### A.6 Components that remain UNKNOWN

| Unknown | Why it is unknown | Searches / probes run that failed to resolve it |
|---|---|---|
| **LLM provider(s), model family, model versions** | Zero provider/model tokens in the client bundle; generation is fully server-side; the Privacy Policy says only "third-party AI providers" (plural). | Token grep over 41 chunks for 18 provider names → 0 hits. |
| **Agent framework** (LangChain/LangGraph/Mastra/custom), agent count, orchestration style | No framework markers client-side; no public disclosure; no leaked prompt. | 25+ web searches incl. `"Synthesise AI" system prompt OR reverse engineered OR tech stack`. |
| **Database engine, ORM, migration tool** | Server-side only; no client identifier. | n/a |
| **Cache / queue / background-job system** | Only framework-level `text/event-stream` reference (Sentry). | n/a |
| **File storage vendor (Azure vs S3 vs R2)** | Only the SAS naming convention and an official Microsoft partnership claim; no hostname. | `generateSasUrl` context grep; no blob host observed. |
| **Billing provider** | Whop checkout is observed for the front-end sale, but the backend entitlement/billing integration (Whop API key, product/plan IDs, webhooks) is not observable. | `/api/trpc` probe for `billing.*`/`credits.*` → 404. |
| **The `/admin/models` and `/admin/ai` surfaces** | Routes exist in the client manifest, but their chunks are not in the anonymous bundle set and the routes are auth-gated. | Attempted `admin.models.get|list`, `admin.ai.get` via tRPC → 404 (names not at that depth). |
| **Mobile app / public API / status page / docs portal** | `status.`, `docs.`, `help.`, `support.`, `api.`, `cdn.` subdomains do not resolve; `/robots.txt` and `/sitemap.xml` on the marketing site list a single page. | DNS + HTTP probes on 6 subdomains → NXDOMAIN/no DNS. |
| **Whether `contentful` (7 hits) and `resend` (12 hits) are genuinely used** | String presence only; no client config, IDs or calls observed. | Single-source; marked low confidence. |

---

## B. Prompt / agent evidence dossier

### B.0 Bottom line

**Nothing about Synthesise AI's prompts or agents is public.** Exhaustive searching found: no leaked system prompt, no agent roster, no agent count, no prompt-injection or LLM-reconnaissance write-up, no published example of a model input, and no user report that reveals a fixed internal stage list at the prompt level. Any agent count or prompt text attributed to Synthesise AI would be fabrication. The one official-sounding document that *does* name agents (`synthesise-ai.gitbook.io`) is a crypto-token artefact contradicted by the live deployment and is rejected (§B.3).

What **is** public is structural, not prompt-level: the route manifest (§A.4), the verified procedure names (§A.5), and a handful of user-visible UI strings. Those support a *behavioural* stage map, not prompt text.

### B.1 Publicly observable stage map (from artefacts, not from prompts)

| Stage / surface | Confirmed status | Responsibility (observed) | User inputs (observed) | Instructions (public prompt) | Tools (observed) | Model | Output | Dependencies | Quality checks | Evidence |
|---|---|---|---|---|---|---|---|---|---|---|
| Flow creation | VERIFIED procedure | Creates the project container for everything else | (server-side schema; not observable) | **None public** | `flow.createFlow` | UNKNOWN | `flow_id` | none | none observed | T06 |
| Audience/creator ingestion | VERIFIED procedure | Pulls source material about the target creator/audience | Instagram handle or YouTube URL/channel | **None public** | `flow.ingestInstagram`, `flow.ingestYoutube`, `flow.listYoutubeVideos` | UNKNOWN | ingested corpus stored on the flow; feed of the creator's videos | a source URL | none observed | T06 |
| "GWOS audit" ingestion | VERIFIED procedure | Parses an audit produced by the sibling product Ghostwriter OS into the flow | (a GWOS audit payload) | **None public** | `flow.parseGWOSAudit` | UNKNOWN | structured audit data | Ghostwriter OS | none observed | T06 |
| Supporting resources | VERIFIED procedure | Adds reference material used as generation grounding | Uploads; YouTube links | **None public** | `resources.upload`, `resources.addYoutubeLinks`, `resources.get` | UNKNOWN | per-flow resource library | a `flow_id` | none observed | T06 |
| **Unique Value Zone (UVZ)** | Route VERIFIED (`/flow/:flow_id/unique-value-zone`); **no dedicated tRPC namespace found** | Positioning/opportunity identification | Skills, expertise, audience problems (per marketing copy) | **None public** | UNKNOWN | UNKNOWN | a UVZ artefact shown at that route | flow context | none observed | T04, T06 |
| **Product charter** | Router VERIFIED (`charter.get`, auth-gated); route VERIFIED | Builds the product blueprint | flow context; product type | **None public** | `charter.get` | UNKNOWN | charter document | a product record | none observed | T04, T06 |
| Product creation | VERIFIED procedure | Instantiates a book/course/coaching product inside the flow | product type + context | **None public** | `product.createForFlow`, `product.get` | UNKNOWN | `product_id` with phase/module tree | `flow_id` | none observed | T04, T06 |
| Reference-material screens | Routes VERIFIED | Per-type grounding (ebook / course / coaching) | user-supplied references | **None public** | UNKNOWN | UNKNOWN | typed reference sets | product | none observed | T04 |
| Structure → phase/module → lesson | Routes VERIFIED | Hierarchical content planning | charter | **None public** | UNKNOWN | UNKNOWN | `phase_id`/`module_id` tree; per-module slide outline | product | none observed | T04 |
| **VTM** screen | Route VERIFIED (`…/vtm` for both products and assets) | Unknown expansion (likely "Value Transformation Map") | UNKNOWN | **None public** | UNKNOWN | UNKNOWN | UNKNOWN | product/asset | none observed | T04 |
| Customisation | Route VERIFIED (`…/customize`) | Branding/format choices | UNKNOWN | **None public** | UNKNOWN | UNKNOWN | customised product | product | none observed | T04 |
| Packaging / export | Routes VERIFIED (`…/package`, `…/export`, `ebook-package`) | Assembles and exports deliverable | UNKNOWN | **None public** | UNKNOWN | UNKNOWN | exported file(s)/bundle | completed product | none observed | T04 |
| Presentation generator (slides) | Procedures VERIFIED | Outline → slides → blocks, with image sourcing | topic + optional per-block regeneration prompt | **None public** (the only public instruction is UI copy) | `presentation.slides.*`, `presentation.outline.get`, `webSearch.unsplashSearch`, `files.generateSasUrl` | UNKNOWN | slide deck; block-level text & images; public share link w/ comments | presentation record | **user-driven**: per-block regenerate / retry image | T05, T06 |
| Store / Whop surface | Routes VERIFIED (`/store`, `/flow/:flow_id/store`, `/whop-app/storefront/:storeId(/product/:storeProductId)`) | Lists/sells products via Whop | pricing/product selection | **None public** | UNKNOWN | UNKNOWN | Whop storefront + product pages | Whop account | none observed | T04 |
| Lead magnet / creator outline / bundle | Routes VERIFIED | Additional deliverable types | UNKNOWN | **None public** | UNKNOWN | UNKNOWN | lead magnet; creator outline; asset bundle | flow | none observed | T04 |
| Admin: models | Route VERIFIED only | Presumably model/provider configuration | — | **None public** | probe for `admin.models.*` → 404 | — | — | — | — | T04, T06 |
| Admin: AI | Route VERIFIED only | Presumably a prompt/agent console | — | **None public** | probe for `admin.ai.get` → 404 | — | — | — | — | T04, T06 |

**The only public instruction-shaped strings in the entire client bundle** are UI copy, not system prompts:

- `"Optionally describe what you want, or leave blank to regenerate from the slide's context."` (per-block regenerate dialog) — T05

No system prompt, no role definition, no guardrail text, and no chain-of-thought scaffolding is publicly retrievable for Synthesise AI.

### B.2 Quality-control mechanisms observable from the outside

| Mechanism | Status | Evidence |
|---|---|---|
| Regeneration (whole slide / whole block / whole deck) | VERIFIED | `presentation.slides.regenerate`, `presentation.blocks.modify`, `blocks.retryImage` |
| Skipping / hiding slides | VERIFIED | `presentation.slides.toggleSkipped` |
| Ordering & grouping of slides | VERIFIED | `presentation.slides.reorder`, `slides.group` |
| Client error reporting to the server | VERIFIED | `log.clientError` (tRPC mutation) |
| Reviewer comments on shared decks | VERIFIED | `presentation.share.comments.create|list` |
| Any automated fact-check, grounding, or quality score | **Not evidenced anywhere** | — |
| Declared AI-accuracy responsibility shifted to the user | OFFICIAL CLAIM | Terms §4: "AI-generated content may contain errors or inaccuracies, and you are responsible for reviewing and verifying all generated content" (T11) |

### B.3 Rejected source: `synthesise-ai.gitbook.io`

This GitBook is **not** linked from `synthesise.ai` (whose sitemap contains only `/` and `/404`) and is not referenced in the app. Its contents — "Rust for core service logic and agent execution", "WASM … on the edge", "PostgreSQL + Redis", "Kubernetes for horizontal scaling", "Stripe webhook processor", four agents named `TutorAgent`/`SEOAgent`/`UXAuditAgent`/`MonetizationAgent`, plus a `$SYNAI` **Solana pump.fun** token with 95 % "liquidity pool" — are internally contradicted by the observable deployment (Next.js 16 on Vercel; Whop, not Stripe, on the observed checkout). Every "Rust example" is a decorative stub (e.g. `fn act(&self, input:&str)->String { format!("Generated outline for '{}'", input) }`), and the navigation even spells its own token section `usdsynai-information`. **Classification: CONTRADICTED / almost certainly AI-generated or crypto-scam-adjacent. Do not cite as operator documentation.** [T13–T17]

---

## C. Reverse-engineering findings

### C.0 Outcome

**No genuine reverse-engineering report of Synthesise AI exists in public.** Exactly **one** project in the surveyed space builds an *alternative* to it, and it is a clean-room concept reimplementation. Everything else is either a name collision, a generic competitor, or unrelated tooling.

### C.1 `Master-Urus/Shadow-Operator` — **inspired implementation / concept-level alternative** (the only relevant hit)

| Attribute | Value |
|---|---|
| URL | https://github.com/Master-Urus/Shadow-Operator |
| Repo description (verbatim) | *"So this is a Python script and an alternative to Ghostwriter OS and Synthesize OS, created by iman gadzhi monetize.com"* |
| Popularity / activity | 1 star, 0 forks, 0 watchers, **2 commits**, last updated Apr 1 (year not stated in the listing) |
| **Description used** | `AI-powered creator intelligence platform with two tools: **Synthesize AI** — Paste Instagram URL → Get digital product ideas + full outline; **Ghostriter OS** — Take product outline → Generate monetization plan + 14-day story sequence` |
| Stack (verified from files) | Next.js **14.2.3** + React 18 + Tailwind 3.4 + TypeScript 5.4 + `lucide-react`; a `scraper-service/` written in **Python/FastAPI**; `docker-compose.yml`; LLM via **Ollama at `http://localhost:11434`** with default model **`qwen3:4b`**; deployment note "Access via Tailscale IP:3000 (no port forwarding needed)" |
| Prompts shipped | `src/lib/prompts.ts` exports `ANALYSIS_PROMPT`, `OUTLINE_PROMPT`, `GHOSTRITER_MASTER_PROMPT`, `GAME_PLAN_PROMPT`, `STORY_SEQUENCE_PROMPT` |
| Prompt lineage | Uses **Hormozi's Value Equation** (`Dream Outcome × Likelihood of Achievement / Time Delay × Effort Required`) and **Cialdini + Jeff Walker's Product Launch Formula**. **Zero references to UVZ, product charter, VTM, phase/module trees, or any Synthesise-specific concept.** |
| Classification | **Inspired implementation (concept-level)** — it copies the *pitch* (paste a creator URL → get a product) and the *marketing frame*, not any Synthesise code, prompts, data model or API. It cannot be a clone: it predates/ignores the observable architecture (tRPC/Better Auth/Whop/Next 16) and runs entirely on a local quantised model. |

**Verbatim third-party prompts (belong to Shadow-Operator, NOT to Synthesise AI — quoted for completeness, no inference about Synthesise may be drawn from them):**

> `ANALYSIS_PROMPT`: "You are an expert digital product analyst. Analyze the provided Instagram creator data and identify digital product opportunities. Analyze the creator's profile and return a JSON object with: niche…, intentGap…, proofGap…, productIdeas: Array of 3-5 product ideas with: title, format (one of "course","ebook","template","workshop","challenge","vault"), rationale, estimatedPrice, targetAudience… Focus on products that: 1. Fill gaps in the creator's current content … 4. Can be monetized at $27-$497 price points. Return JSON only."

> `OUTLINE_PROMPT`: "You are a product architect specializing in digital courses using the Value Equation framework… Structure the product for Micro-Learning where the first module delivers a Quick Win in under 15 minutes… 1. Module 1 must deliver a 'Quick Win' … 4. Maximum 6-8 modules, 5-8 lessons per module. Return JSON only."

> `GHOSTRITER_MASTER_PROMPT`: "You are Ghostriter OS, a psychological precision marketing system combining the principles of Robert Cialdini with Jeff Walker's Product Launch Formula. CRITICAL RULES: - NEVER use these banned words: unleash, delve, elevate, unlock, game-changer … - If data is missing, use [NEED_DATA: Topic] placeholder - Write as a doctor diagnosing, not a salesperson pitching…"

[VERIFIED][high][T26,T27,T28,T29]

### C.2 Classified survey of every other candidate hit

| Project / artefact | Platform | Classification | Evidence supporting the classification |
|---|---|---|---|
| `S3th5043/SynthesiseAI`, `S3th5043/SynthesiseAI2`, `ahsanahtesham/SynthesiseAI` | GitHub | **Name collision (unrelated)** | Empty/README-less repos (raw README fetch → HTTP 404); no connection to Gadzhi, no digital-product framing |
| `esratankumar/synthesise-ai` | GitHub | **Name collision (unrelated)** | README is the unmodified `create-next-app` boilerplate ("This is a Next.js project bootstrapped with `create-next-app`"); no product logic |
| `abhaymaknikar74-spec/synthesise-ai` | GitHub | **Name collision (unrelated)** | Description: "AI content synthesis platform with Ollama integration" — different domain |
| `monetiseos.com` + `app.monetiseos.com` | Web / Next.js app | **Generic competitor targeting the same buyer** | Sells an "AI Shadow Operator" tool; meta-keywords literally `"synthesise AI alternative, ghostwriter OS alternative"`; 5 steps (Creator Monetisation Analysis → Full Digital Product → Marketing Funnel → 14-Day Launch Plan → Creator Pitch); paste-an-Instagram-or-YouTube-URL input; ships a 0–100 creator "score" and demand ratings (e.g. `74 score`, `HIGH DEMAND · 9/10`, `$22K–$68K 1st 3 months`). Different backend shape: `Server: Vercel` but `/api/trpc` → 404 and `/api/auth/get-session` → 404 |
| `Master-Urus/Shadow-Operator` | GitHub | **Inspired implementation** (see C.1) | Self-described alternative; local Ollama; no Synthesise internals |
| `aentrepreneur/ai-monetize` | GitHub | **Generic competitor (unrelated)** | Spanish-language FastAPI/SQLAlchemy/Celery/Redis/Postgres pipeline with an 8-stage VALIDATE→ANALYTICS flow and 5 social publishing providers; no reference to Gadzhi or Synthesise |
| `Shital24650/CreatorOS` | GitHub | **Generic competitor (unrelated)** | "AI-powered content compiler" turning one creator source into a 7-day campaign; no reference to Synthesise |
| `ghostwriteros.ai` | Web (Framer) | **Sibling product, same operator** | `Server: Framer/26fa766` (identical build string to `synthesise.ai`); referenced by a consultant's profile as "Synthesise AI / GhostWriterOS" |
| `flozy.com` / `dashboard.flozy.com` | Web (Vercel) | **Third-party partner CRM** | Flozy is an independent company (own LinkedIn page, own case studies); has an "Educate X Flozy" page; referenced by a reviewer as "Monetise's CRM". No code relationship to Synthesise observed |
| `gwos.net`, Prometheus "GWOS-AI" | Web | **Name collision (unrelated)** | Unrelated blockchain/maintenance-planning products |
| `npow/ghostwriter`, `kreddys/ghostwriter`, `angelarose210/ghostwriter`, `OneSpiral/ghost-writer`, `PuvaanRaaj/ghostwrite`, `leozc/ghostwriters`, `Ashwinpillai9/Ghostwriter` | GitHub | **Name collisions (unrelated)** | None reference Gadzhi's Ghostwriter OS; `Ashwinpillai9/Ghostwriter` is a "Wispr Flow alternative" for Windows |
| `synthesise-ai.gitbook.io` | GitBook | **Unaffiliated / unverifiable; contains fabricated-looking technical claims** | Not linked from the official site; describes a Rust/Kubernetes system and a Solana `$SYNAI` token contradicted by the live Next.js/Vercel/Whop deployment (§B.3) |
| Hugging Face | Models API | **NONE FOUND** | `https://huggingface.co/api/models?search=synthesise&limit=20` → `[]` |
| npm | Registry search | **NONE FOUND** | Search for `synthesise` returns unrelated packages (`@verevoir/design-system`, `tokonomix-council-mcp`, `@dittli/tts-core`, `node-red-contrib-music`, `@finos/calm-cli`) |
| GitHub **code** search for `"synthesise.ai"` | GitHub | **BLOCKED** | GitHub returns "Sign in to search code on GitHub"; this is a recorded limitation, not a negative result |
| GitHub **issues** for `"synthesise ai" OR "monetise ai"` | GitHub | **NONE FOUND** | 155 results, all generic uses of the words "monetise"/"synthesise" (Omnihash GPU tokens, llms-txt access control, subtitle monetisation, WordPress taxonomies, HolmesGPT, OpenSfM…). Zero references to the product |
| PyPI | Registry | **NONE FOUND** | Search surfaced only unrelated synthetic-data packages (`synthadoc`, `knowlyr-datasynth`, `synthpop-py`, `synthscholar`) |

### C.3 Searches run for section C (and their outcome)

Distinct queries executed via web search (25 total across this dossier) and GitHub/registry endpoints:

1. `"Synthesise AI" Iman Gadzhi` → official + review ecosystem only
2. `"synthesise.ai" login app` → found `app.synthesise.ai`
3. `github "synthesise ai" OR "synthesise.ai" clone` → **nothing relevant**
4. `"Synthesise AI" system prompt OR "reverse engineered" OR "tech stack"` → generic prompt-leak literature only (Claude Code, v0); **no product hit**
5. `synthesise.ai builtwith OR wappalyzer technology` → only unrelated `synthesia.io` tech pages; **no third-party tech profile of synthesise.ai exists**
6. `"IAG Services FZCO" Iman Gadzhi Dubai` → operator entity + ecosystem domains (educate.io, monetise.com, consulting.com)
7. `github "unique value zone" OR "product charter" AI generator iman gadzhi` → **nothing relevant**
8. `npm OR pypi "synthesise" package AI digital product generator` → **nothing relevant**
9. `"Synthesise AI" reddit review legit` → review sites + Reddit threads about the parent programme
10. `"Synthesise AI" price per month OR $97 OR subscription` → WhopScope $97/mo
11. `"synthesise ai" prompt OR "system prompt" OR leak OR injection` → **NONE FOUND** (returned unrelated AI/RAG literature)
12. `"Synthesise AI" help centre OR FAQ OR "how it works" educate` → no help centre exists
13. `Next.js "_next/static/immutable/chunks" version` → version-pinning evidence (Next 16.3+)
14. `"Synthesise AI" team engineer OR CTO OR founder "Ghostwriter OS"` → one consultant profile
15. `"synthesise ai" affiliate OR trustpilot OR scamadviser OR refund` → affiliate review farm + Trustpilot for monetise.com
16. `"Synthesise AI" "unique value zone" OR UVZ OR "transformation map" steps` → marketing/affiliate copy only
17. `gitlab OR "hugging face" OR "npm" synthesise.ai digital product generator iman gadzhi` → **nothing relevant**
18. `"Ghostwriter OS" Iman Gadzhi price review 2026 platform` → sibling product (ghostwriteros.ai)
19. `x.com OR twitter.com "Synthesise AI" gadzhi built OR launched` → **no technical content**
20. `"Ghostwriter OS" "Monetise OS" "Synthesise AI" reddit AskMarketing` → **NONE FOUND**
21. `"Flozy" Monetise CRM Iman Gadzhi software` → independent CRM, no Synthesise integration evidence
22. `"monetiseos" OR "ghostwriteros" OR "flozy" clone github python alternative` → competitor + name collisions
23. `"GWOS" OR "parseGWOSAudit" OR "ghostwriter audit" synthesise monetise` → **nothing relevant** (GWOS hits are unrelated)
24. `"Synthesise AI" "Microsoft Founders Hub" OR "Microsoft for Startups"` → **no corroborating page from the operator** (only Microsoft's own programme pages)
25. `"synthesise ai" API OR integration OR webhook OR zapier OR make.com` → **no public API, docs or integration surface**

GitHub/registry endpoints opened directly: `github.com/search?q=synthesise+ai&type=repositories` (147 unrelated results), `…q=iman+gadzhi&type=repositories` (13 results; 1 relevant), `…q=ghostwriter+os+OR+monetise+OR+synthesise+alternative&type=repositories` (2 results; 1 relevant), `…q="synthesise.ai"&type=code` (**login-gated**), `…q="synthesise ai"+OR+"monetise ai"&type=issues` (155 unrelated), `huggingface.co/api/models?search=synthesise` (`[]`), `registry.npmjs.org/-/v1/search?text=synthesise`.

---

## D. What a disciplined ~6-month-old app of this type most plausibly looks like — **explicit INFERENCE**

> **This entire section is labelled INFERENCE.** It is a reasoned reconstruction constrained by the verified observations above, intended as a build blueprint, not as a claim about Synthesise AI's actual code. Nothing here should be cited as fact.

**D.1 Why "~6 months" is a plausible age.** Framer publish stamps on the marketing site and both policy pages are all **2026-09-09**, consistent with a marketing-surface rewrite/launch in **September 2026**; the Whop storefront asset path carries a `2026-04-28` and `2026-05-16` upload timestamp; the app's own routes carry no dated artefacts. A codebase whose shared chunks contain *both* mature product surface (`presentation.*`, `course.get`, `admin/users-management`, Whop storefronts) *and* a live `/dev/*` R&D route group (`/dev/brain`, `/dev/jev`, `/dev/uvz`, `/dev/ebook`, `/dev/image-loader`, `/dev/lead-magnet`, `/dev/storefront`) reads like a **fast-moving 6–12 month old product still exploring its own feature space** — exactly the "internal tools shipped to prod under `/dev`" pattern. [INFERENCE][medium][T01,T04,T11,T12,T19]

**D.2 The plausible architecture (inference, matching the verified surface).**

- **Repo:** a single Next.js App Router + TypeScript monorepo (Turbopack build), Tailwind + shadcn/ui, tRPC routers named after the domain (`flow`, `product`, `charter`, `course`, `resources`, `files`, `presentation`, `webSearch`, `log`, `admin`) mounted under one `/api/trpc` handler with a superjson transformer and Zod inputs. This is the canonical shape of the surface actually observed.
- **Data model:** `flow` (a project scoped to one creator/audience) → zero-or-more `digital_product` records typed as **ebook | course | coaching**, zero-or-more `digital_asset` records (bundles), and free-standing `presentation` records; products carry a `phase → module → lesson` tree plus per-type "references" and a `vtm` screen; a `charter` is a per-product generated artefact addressed by its own router.
- **Generation pattern:** almost certainly **not** a single mega-prompt. The verified router names imply a **stepwise, persisted, re-runnable pipeline**: cheap synchronous bits (create flow, create product, load a list) plus long-running mutations (`ingestYoutube`, `ingestInstagram`, `parseGWOSAudit`, `generateSasUrl`, `unsplashSearch`, `slides.create`, `slides.regenerate`). The presence of `presentation.blocks.retryImage` and `slides.regenerate` with an **optional user prompt** is the signature of a **per-node regenerate endpoint** rather than a monolithic chain — the single most copyable architectural decision here.
- **Grounding by ingestion rather than by retrieval-at-answer-time:** the inputs are *ingested first* (creator profile/videos, uploaded resources, YouTube links) and then referenced by later steps — i.e. the "context" is materialised into the flow before generation, instead of being fetched at generation time. The **complete absence** of any retrieval/vector-store identifier client-side is consistent with either server-side retrieval or plain long-context stuffing.
- **Auth/entitlement:** Better Auth with the Whop OAuth provider as the social sign-in, plus an email-OTP path keyed to "the email you purchased Monetise with" — i.e. **entitlement derived from purchase**, checked at the API boundary (every product/charter/files/resources query returned `-32001` before any schema validation, which is consistent with a router-level auth middleware rather than per-field guards).
- **Storage:** a two-phase upload: mint a short-lived **SAS/presigned URL** (`files.generateSasUrl`), have the browser PUT/upload directly to the object store, then re-read via `files.get`. This keeps large binaries off the serverless function.
- **Observability:** Sentry for exceptions/spans, Clarity for session replay/heatmaps, Segment (and a stray Amplitude) for product analytics, plus a first-party `log.clientError` tRPC mutation — a belt-and-braces stack typical of a team that has already been burned by silent client failures.
- **Shipping cadence:** an `/admin/*` surface for users, products, stores, models and AI config implies a small operations team that tunes prompts/model routing **without a deploy** — consistent with `/admin/models` and `/admin/ai` existing as routes while their chunks are not in the public bundle.

**D.3 The disciplined 6-month build order an engineer would follow (INFERENCE).**

1. **Weeks 0–3 — skeleton that can persist state:** Next.js App Router + tRPC + Zod + superjson; one `flow` table; Better Auth + Whop provider; entitlement middleware that runs **before** input validation so unauthenticated callers get a uniform 401.
2. **Weeks 2–6 — ingestion before generation:** creator/URL ingestion, downloads, transcript extraction, and a per-flow resource library. Persist everything; never let a generation step depend on a live fetch.
3. **Weeks 4–10 — stepwise generation with per-node regeneration:** one router per artefact class; every generated node gets an ID and can be regenerated *on its own* with an optional user steer. This is what makes the product feel editable rather than slot-machine.
4. **Weeks 6–14 — the artefact tree and export:** phase/module/lesson tree per product type; editor → package → export; a share token for read-only public viewing.
5. **Weeks 8–16 — the second product family (presentations/slides)** reusing the same ingestion + regenerate primitives, with object-store uploads and an image-sourcing service (Unsplash-style) plus an image retry path.
6. **Weeks 12–20 — admin, observability, billing hooks:** an internal admin for users/products/models, Sentry + a `clientError` sink, and a checkout via an existing merchant-of-record platform (Whop) rather than building payments in-house.
7. **Throughout — an explicit escape hatch:** the `/dev/*` route group and the `/design-system` route are the honest signatures of a team that builds in public to itself. They are cheap and, as this dossier shows, **disclosive** — a fact the operator should weigh.

**D.4 Explicit non-inferences.** Nothing in the evidence supports any claim about: the number of agents, whether an "agent" abstraction exists at all as opposed to plain prompt chains; which model provider(s) are used; whether UVZ involves any real ML (embeddings, clustering, ranking) versus an LLM prompt with a scoring-looking output; whether external market data is queried; or whether any hosted RAG/vector store exists.

---

## Appendix — method limitations (recorded honestly)

1. **No authenticated session.** Every authenticated screen, and every generation output, is unobserved. All "stage" descriptions are structural inferences from route/procedure names, not from watching a run.
2. **GitHub code search is login-gated.** `github.com/search?...&type=code` returns "Sign in to search code on GitHub". This is *not* a negative result: a fork or clone could exist in indexed code without being findable here. Repo/issue search **was** performed and is reported.
3. **The client bundle set is partial.** Only chunks referenced by anonymous pages (`/signin`, `/share/[token]`) were retrievable. Feature chunks behind auth (dashboard, editor, admin) were never served, so their libraries, hooks and string literals are unknown. Where a library is asserted, it is because its code is present in a *served* chunk.
4. **Chunk class hashes rotate per deployment.** The observations are tied to `dpl_4U29zfpU2gSrZBJQtA6KJewkTvZt` on 2026-10-11 and may not be reproducible later; the **method** (paths, headers, tRPC error codes) will be.
5. **One file-transfer incident:** the shell's `/tmp` mapping behaved flakily (curl writes to POSIX `/tmp` paths intermittently failed). Downloads were redirected to a Windows path and the temporary probe directory was removed after analysis; no application code was modified.
6. **Secondary/affiliate sources are not evidence.** The two "teardown"-style reviews cited (T32, T33) carry explicit affiliate disclosures, and the one that claims Claude 3.5 Sonnet / GPT-4o heritage states it as inference ("similar to"), not observed fact. It is recorded as USER-REPORTED/low and is **not** used to support any stack claim.
