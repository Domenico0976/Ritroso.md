---
name: "05_COMPONENTS.md"
description: "Technical architecture for uptapix.bid — layered monolith with Next.js App Router, Supabase auth, Stripe payments, PostgreSQL for data. Injection rules from code-quality and security-defensive categories applied."
---

# 05_COMPONENTS.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Architecture pattern
**Layered monolith** — all components live in a single Next.js deployment. Justification: single-founder, limited budget, no need for independent scaling of channels. Anti-pattern rejected: microservice architecture — unnecessary operational overhead for a product with two tightly coupled revenue streams and no independent traffic patterns.

## Tech stack
| Layer | Technology (real name) | Version/Tier | Justification (constraint or element it serves) |
|-------|----------------------|--------------|------------------------------------------------|
| Frontend | Next.js | 15.x (App Router) | SSR for landing page SEO; client components for interactive canvas; built-in routing |
| Styling | Tailwind CSS | 3.x | Rapid UI development; design system tokens for consistency across both channels |
| Backend | Next.js Server Actions | 15.x | Eliminates separate API layer; keeps changelog CRUD and canvas operations in one codebase |
| Database | PostgreSQL (Neon) | latest stable | Relational data with row-level security; handles both channels' data uniformly |
| Auth | Supabase Auth | free tier | Email magic link; social login ready; built-in row-level security for data isolation |
| Payments | Stripe Checkout | standard | One-time payments; webhook reliability; no subscription complexity for MVP |
| Storage | Supabase Storage / AWS S3 | free tier | Canvas image uploads; changelog cover images; ≤5MB per file |
| Hosting | Vercel | hobby tier | Zero-config Next.js deployment; automatic HTTPS; free tier covers early traffic |
| Monitoring | Sentry | free tier | Error tracking across both channels; alerts on payment webhook failures |
| Analytics | Plausible | privacy tier | Simple page view tracking; no cookie consent required; GDPR-friendly |

## Component contracts

### marketing-site
- **Responsibility**: Landing page and public-facing content explaining both channels
- **Inputs**: Static copy, pricing data, channel descriptions
- **Outputs**: Rendered HTML pages; email capture form submission
- **Failure mode**: Server renders broken layout or wrong pricing — reference 10_ERROR Failure 1
- **Dependencies**: None (static)

### auth-and-dashboard
- **Responsibility**: User authentication, session management, unified dashboard showing both channels' assets
- **Inputs**: Email address (magic link) or OAuth token from provider
- **Outputs**: Authenticated session; dashboard with changelog entries + canvas blocks tabs
- **Failure mode**: Session hijack or unauthorized access to another user's data — reference 10_ERROR Failure 3
- **Dependencies**: Supabase Auth; PostgreSQL users table

### changelog-service
- **Responsibility**: Create, validate, moderate, and publish changelog entries
- **Inputs**: Title, description, CTA link, tier selection, payment confirmation
- **Outputs**: Published entry in the public feed; draft/rejected states in admin queue
- **Failure mode**: Unmoderated spam or inappropriate content published — reference 10_ERROR Failure 4
- **Dependencies**: auth-and-dashboard (user ID); payment-service (payment confirmation)

### canvas-service
- **Responsibility**: Manage canvas grid state, block ownership, rendering, and conflict prevention
- **Inputs**: Block selection, user payment confirmation, render data (color, text, image)
- **Outputs**: Updated grid state; rendered block visible to all users
- **Failure mode**: Double-booking the same block — reference 10_ERROR Failure 2
- **Dependencies**: auth-and-dashboard (user ID); payment-service (payment confirmation)

### payment-service
- **Responsibility**: Create Stripe Checkout sessions, handle webhooks, reconcile payments to orders
- **Inputs**: Channel type (changelog/canvas/bundle), tier or block coordinates, user ID
- **Outputs**: Stripe checkout URL; webhook-confirmed order record
- **Failure mode**: Payment confirmed but order not recorded (webhook loss) — reference 10_ERROR Failure 3
- **Dependencies**: None external; writes to PostgreSQL orders table

### admin-console
- **Responsibility**: Moderate changelog submissions, manage canvas content takedowns, view basic stats
- **Inputs**: Admin authentication; action (approve/reject/takedown)
- **Outputs**: Updated entry status; audit log entry
- **Failure mode**: Admin action applied to wrong entry — reference 10_ERROR Failure 4
- **Dependencies**: changelog-service; canvas-service

### analytics-layer
- **Responsibility**: Track page views, conversion events, and revenue per channel
- **Inputs**: Page view events; checkout start/complete events
- **Outputs**: Aggregated metrics in dashboard; exportable CSV
- **Failure mode**: Events lost due to tracking script failure — non-critical, data is approximate
- **Dependencies**: None (writes to separate analytics table or external service)

## Injected rules — code-quality [SKILL:code-quality]
- All server actions must have explicit error handling with user-facing messages [SKILL:code-quality]
- Database queries must use parameterized statements — no string concatenation for SQL [SKILL:code-quality]
- Payment webhook handlers must be idempotent — same event processed twice must not create duplicate orders [SKILL:security-defensive]
- Canvas block reservation must use database-level row locking or unique constraints to prevent double-booking [SKILL:security-defensive]

## Injected rules — security-defensive [SKILL:security-defensive]
- All user input (title, description, text on canvas) must be sanitized before database storage [SKILL:security-defensive]
- Canvas image uploads must be validated for file type (image/* only) and size (≤5MB) [SKILL:security-defensive]
- Stripe webhook secret must never be exposed to the client — validation happens server-side only [SKILL:security-defensive]
- Admin actions must be logged with timestamp, actor, and target entry ID for audit trail [SKILL:security-defensive]

## Single point of failure
- Component: **Stripe**
- Failure mode: Stripe API downtime or webhook delivery failure prevents payment confirmation
- Cascade: Users cannot complete purchases → changelog entries and canvas blocks remain unpaid → revenue stops entirely
- Detection: Stripe status page check; webhook retry counter in payment-service; alert threshold at 3 failed webhooks in 10 minutes
- Mitigation status: PARTIAL (Stripe has 99.99% uptime; fallback is manual order creation via admin console if Stripe is down)

## Forbidden patterns
- Storing Stripe secrets in client-side code or environment variables exposed to the browser — reason: security vulnerability, leads to credential theft [SKILL:security-defensive]
- Using client-side-only payment verification (no webhook) — reason: users can fake payment success, reference 10_ERROR Failure 3 [SKILL:security-defensive]
- Rendering the full canvas grid as individual DOM elements for every block — reason: performance degradation at 100+ blocks, causes 10_ERROR Failure 2 cascade [SKILL:code-quality]
- Allowing unmoderated auto-publishing without any content filter — reason: spam and abuse would destroy platform credibility, reference 10_ERROR Failure 4 [SKILL:security-defensive]
