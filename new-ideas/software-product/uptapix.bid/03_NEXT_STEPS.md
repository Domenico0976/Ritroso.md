---
name: "03_NEXT_STEPS.md"
description: "Operational roadmap for uptapix.bid P1 MVP. Three phases: landing validation, changelog hub build, canvas engine build. Every step has a named owner, concrete output, and sign-off condition."
---

# 03_NEXT_STEPS.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Phase 1 — Landing Validation
**Goal**: Confirm that the dual-channel value proposition resonates with target users before any code is written.
**Duration**: 1 week
**Budget**: €0 (no-code landing page + manual outreach)
**Sign-off condition**: At least 5 people voluntarily provide email on the landing page, or 2 express willingness to pay via direct message.

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 1.1 | Write landing page copy explaining both channels, pricing tiers, and how they work independently | Founder | `/public/index.html` with hero, feature sections, pricing table, email capture form | Copy covers both channels clearly; no jargon; non-technical reader understands in <2 min |
| 1.2 | Deploy landing page to a free host (Vercel, Netlify, or GitHub Pages) | Founder | Live URL accessible from any browser | URL responds with correct content on desktop and mobile |
| 1.3 | Share landing page in 3 relevant communities (Indie Hackers, r/saas, one Discord server) | Founder | 3 posts with link + brief context | Posts are live; track click-through and email signups for 7 days |
| 1.4 | Collect and categorize feedback from replies and emails | Founder | Spreadsheet with: email signups, reply themes, willingness-to-pay signals | At least 5 emails collected OR 2 explicit "I'd pay for this" signals |

## Phase 2 — Changelog Hub MVP
**Goal**: A working changelog submission flow with payment and basic moderation.
**Duration**: 3 weeks
**Budget**: €800–1,200 (founder dev time + Stripe + hosting)
**Sign-off condition**: A test user can go from landing page → select tier → pay → publish entry → see it live in the hub.

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 2.1 | Set up Next.js project with App Router, Tailwind CSS, PostgreSQL (Neon) | Founder | Project scaffold with auth (Supabase), database schema for changelog entries | `next dev` runs; DB connection confirmed; auth middleware protects `/dashboard` |
| 2.2 | Build changelog submission form (title, description, CTA link, tier selection) | Founder | Client-side form with validation; drafts saved to DB on submission | Form rejects invalid input; draft persists on reload |
| 2.3 | Integrate Stripe Checkout for one-time payments with webhook handling | Founder | Working checkout flow; webhook verifies payment and changes entry status to `paid` | Test mode: payment succeeds → entry status updates; webhook idempotent |
| 2.4 | Build changelog feed page (public, paginated, sorted by newest) | Founder | `/changelog` page showing published entries with tier badge | Page loads < 1s; entries display title, description excerpt, CTA, tier |
| 2.5 | Build admin moderation queue (approve/reject with optional note) | Founder | `/admin` page listing `pending` entries with approve/reject buttons | Admin can approve → entry goes live; reject → entry moves to `rejected` |
| 2.6 | Deploy to Vercel with production Stripe keys and DB connection | Founder | Live staging URL; test user can complete full flow end-to-end | End-to-end test passes: landing → form → pay → see entry in feed |

## Phase 3 — Canvas Engine MVP
**Goal**: A working interactive canvas where users can buy and render blocks.
**Duration**: 3 weeks
**Budget**: €1,200–1,800 (founder dev time + hosting + storage)
**Sign-off condition**: A test user can select an available block, pay, and render content in it — with no double-booking.

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 3.1 | Design canvas grid schema (blocks, zones, ownership, coordinates) | Founder | Database tables: `canvas_blocks`, `canvas_ownership`, `canvas_content` | Schema supports atomic reservation (row-level lock or unique constraint) |
| 3.2 | Build interactive canvas viewer (grid rendering, zoom, block selection) | Founder | Canvas component with selectable blocks; selected block highlights with price | Grid renders < 2s; selection works without page reload |
| 3.3 | Implement block reservation lock during checkout flow | Founder | Block locked for 10 minutes during Stripe checkout; unlocks on timeout or payment | Two simultaneous selectors never both succeed; timeout releases lock correctly |
| 3.4 | Build block editor (color fill, text overlay, simple image upload) | Founder | Editor panel with color picker, text input, image upload; preview updates in real-time | Editor produces valid render; image upload < 5MB; text respects block dimensions |
| 3.5 | Add canvas to dashboard (show user's owned blocks with edit/delete) | Founder | Dashboard tab listing owned blocks; each block shows thumbnail + edit button | User sees only their blocks; edit navigates to editor; delete confirms before acting |
| 3.6 | Deploy canvas MVP; run end-to-end test with test user | Founder | Live staging URL; test user completes full canvas flow | End-to-end test passes: select → pay → render → view in public canvas |

## Phase 4 — Polish & Launch
**Goal**: Ship a production-ready MVP with basic analytics and moderation.
**Duration**: 2 weeks
**Budget**: €400–600 (hosting, monitoring tools)
**Sign-off condition**: Both channels working in production; admin can moderate; basic analytics tracking live.

| # | Step | Owner | Output | Done when |
|---|------|-------|--------|-----------|
| 4.1 | Add basic analytics (page views, conversion rate per channel) | Founder | Simple event tracking via Plausible or similar; dashboard shows key metrics | Event tracking fires on checkout start and completion |
| 4.2 | Harden admin moderation with rejection templates and audit log | Founder | Pre-written rejection reasons; all moderation actions logged with timestamp | Admin can reject with reason; log queryable for any entry |
| 4.3 | Production deployment with monitoring and error alerts | Founder | Vercel production deploy; Sentry or equivalent error tracking; uptime monitoring | Errors trigger alert within 5 minutes; dashboard shows last 24h error count |
| 4.4 | Final end-to-end test with real payment (€1 test transaction) | Founder | Verified production payment flow; refund path tested | Real Stripe test transaction completes; refund process documented |

## Dependencies and blockers
- Landing validation (Phase 1) must succeed before Phase 2 begins — if no one signs up, rebuilding the hub is wasted effort
- Stripe account setup blocks all payment-related steps in Phases 2 and 3 — apply early
- Database schema for canvas (Phase 3.1) depends on lessons learned from changelog schema (Phase 2.1) — can begin design in parallel

## Decisions deferred
| Decision | Deferred to | Reason | Blocking? |
|----------|-------------|--------|-----------|
| Exact canvas grid size (e.g. 100×100 vs 200×200) | Phase 3.1 | Depends on performance testing with real browser | No |
| Color/brand guidelines for the platform | Phase 4 | Can use neutral palette for MVP; refine after validation | No |
| Revenue share or affiliate program for changelog publishers | P2 or later | Not needed for MVP; focus on direct sales first | No |
