---
name: "04_ELEMENTS.md"
description: "Inventory of required elements for uptapix.bid, ordered by launch criticality. Critical elements are required for P1; Important elements are P2; Optional are P3 or never."
---

# 04_ELEMENTS.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Critical (P1 — must exist at launch)
| Element | Why critical | Owner | Dependency |
|---------|-------------|-------|------------|
| Landing page explaining dual channels | Without it, no user knows what the product is or how to use it | Founder | None — first thing built |
| Shared authentication system | Both channels share the same account; different accounts break the independence guarantee | Founder | None |
| Stripe payment integration | No payment = no revenue; both channels require it | Founder | Landing page for traffic |
| Changelog submission form | Core P1 feature — the primary revenue driver | Founder | Auth + Payment |
| Changelog feed (public, paginated) | Users must be able to see published entries to understand value | Founder | Submission form + DB |
| Admin moderation queue | Content must be reviewed before publishing to prevent abuse | Founder | Submission form |
| Canvas grid viewer (read-only) | Users must see available blocks before purchasing | Founder | None — can be static initially |
| Canvas block purchase flow | Core P1 feature — secondary revenue driver | Founder | Auth + Payment + Grid viewer |
| Canvas block editor (basic) | Purchased blocks must be editable or the purchase is worthless | Founder | Block purchase flow |
| Shared database schema | Both channels store data; a unified schema prevents integration debt | Founder | None — designed first |

## Important (P2 — required for full product)
| Element | Why important | Owner | Blocked by |
|---------|--------------|-------|------------|
| Bundle checkout (single payment for both channels) | Increases average order value; users who like one channel often want both | Founder | P1 payment system |
| Canvas rent periods (time-limited ownership) | Enables lower entry price; attracts casual buyers | Founder | P1 canvas engine |
| Public profile pages | Gives buyers a persistent identity; drives repeat visits | Founder | P1 auth system |
| Scheduled publishing for changelogs | Increases utility for users who plan launches in advance | Founder | P1 changelog system |
| Basic analytics dashboard | Founder needs to see which channel converts better | Founder | P1 deployment |
| Email notifications (purchase confirmation, publish approval) | Reduces support load; improves user experience | Founder | P1 auth + payment |

## Optional (P3 or never)
| Element | Rationale for deferral |
|---------|----------------------|
| Multiplayer canvas collaboration | Over-complicates the MVP; single-user ownership is simpler to build and validate |
| Auction mechanics for premium canvas zones | Adds complexity without clear demand; block purchase at fixed price is sufficient for P1 |
| API access for third-party integrations | No evidence of demand; would increase maintenance burden significantly |
| Native mobile app | Web-first is sufficient; mobile browsers handle the canvas adequately |
| Community features (comments, follows) | Out of scope for a visibility platform; introduces moderation overhead |

## Element interaction map

- Landing page requires the shared authentication system to be in place before user signups can be captured meaningfully.
- Changelog submission requires both the shared database schema and Stripe payment integration to be operational — a submission without payment capture is a broken flow.
- Canvas block editor requires the canvas block purchase flow to have successfully completed — you cannot edit a block you do not own.
- Admin moderation queue requires the changelog submission form to create entries in `pending` state — without submissions, the queue is empty.
- Shared database schema must be designed before either the changelog or canvas database tables are created — changing the schema mid-build causes data integrity issues.

## Injected elements (from active skills)
- Landing page copy must follow benefit-first framing (not feature-first) [SKILL:copywriting-content]
- Canvas visual design must include loading, empty, and error states for each interaction [SKILL:ui-ux-design]
- Payment webhook handling must include idempotency and retry logic [SKILL:api-backend]
- Database schema must include audit logging for all ownership changes [SKILL:security-defensive]
