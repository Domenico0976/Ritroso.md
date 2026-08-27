---
name: "02_PRODUCT.md"
description: "Product specification for uptapix.bid — two independent visibility channels (changelog hub and pixel canvas). User flows for each channel plus the dual-usage case. Constraints: web-only, no subscription, simple moderation."
---

# 02_PRODUCT.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## What it does — one paragraph

uptapix.bid is a web platform with two independent ways to buy public visibility. The **changelog hub** lets makers and startups publish product updates behind a small fee, appearing in a curated feed alongside other published entries. The **pixel canvas** lets anyone buy a block of pixels on a public digital wall and fill it with color, text, or a simple graphic. A user can use one channel, both, or neither — the systems share only an account and payment infrastructure, never a workflow.

## User flows — P1 (mandatory, min 3 flows)

### Flow 1: Publish changelog entry

- Entry point: user lands on changelog hub page, sees available tiers and submission form
- Step 1: user fills title, description, CTA link, and chooses tier → system validates input length and format
- Step 2: user clicks submit → system presents payment summary with tier price
- Step 3: user completes Stripe checkout → system creates entry in `draft` state, queues for light review
- Outcome: entry appears in the hub feed within minutes (auto-approved for clean content) or after brief manual review
- Error state: if payment fails, entry remains in `draft` and user is notified to retry

### Flow 2: Buy canvas block

- Entry point: user opens the canvas, sees a grid of available blocks with pricing by zone
- Step 1: user selects an available block → system shows price, confirms ownership is free
- Step 2: user clicks "Buy" → system presents payment summary
- Step 3: user completes Stripe checkout → system creates `paid` ownership record, unlocks editing
- Outcome: user can now render their block with color, text, or a simple image
- Error state: if two users select the same block simultaneously, the second gets a "block taken" error and is redirected

### Flow 3: Dual-channel dashboard

- Entry point: authenticated user opens their dashboard
- Step 1: user sees two panels — "My changelog entries" and "My canvas blocks" — completely separate
- Step 2: user clicks either panel to manage that channel independently
- Outcome: user can view stats, edit drafts, or extend canvas ownership without touching the other channel
- Error state: N/A — dashboard is read-only aggregation, no failure mode

## User flows — P2 (planned, not built in P1)

- Bundle checkout: single payment for both a changelog entry and a canvas block
- Scheduled publishing: queue changelog entries for future dates
- Canvas rent periods: time-limited blocks that expire automatically
- Public profile pages: persistent URL per buyer showing their canvas art and changelog history
- Featured spot rotation: paid placement at top of changelog feed for 24h

## Feature scope

### P1 — Must ship
| Feature | Description | Acceptance criteria |
|---------|-------------|---------------------|
| Changelog submission form | Title, description, CTA link, tier selection | Form validates input; entry created on payment success |
| Canvas grid viewer | Interactive grid showing available/paid blocks with zone pricing | Grid renders in browser; selection locks during checkout |
| Stripe payment integration | One-time checkout for both channels | Payment webhook confirmed before granting access |
| Shared auth system | Email magic link or social login | Single account covers both channels |
| Admin moderation queue | Simple approve/reject for changelog entries | Admin can view pending entries and act within 24h |
| Landing page | Explains both channels, pricing, and how they work | Non-technical visitor understands the dual model in <2 min |

### P2 — Planned next
| Feature | Description | Depends on |
|---------|-------------|------------|
| Bundle checkout | Single payment covering both channels | P1 payment system stable |
| Canvas rent periods | Time-limited block ownership | P1 canvas rendering engine |
| Public profile pages | Persistent buyer identity | P1 auth system |
| Scheduled publishing | Queue changelog entries | P1 changelog moderation system |

### Out of scope
- Multiplayer canvas (real-time collaboration)
- Native mobile app
- Subscription billing
- Third-party API
- Community features (comments, follows)
- Auction mechanics
- Analytics dashboard beyond basic view counts

## System requirements

- Platform: web (responsive, desktop-first)
- Minimum viable device: Chrome/Firefox/Safari latest, mobile browser acceptable
- Offline support: no
- Accessibility: WCAG 2.1 AA for core flows (form submission, payment, navigation)
- Performance target: page load < 2s on 4G; canvas grid renders within 1s
