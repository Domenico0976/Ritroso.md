---
name: "08_LIMITS.md"
description: "Hard limits for uptapix.bid — structural, technical, and injected limits from active skills. Every limit tagged with [SKILL:name]. Limits are derived from failure modes in 10_ERROR and scope constraints from 01_GOAL."
---

# 08_LIMITS.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Hard limits — scope
- The two channels (changelog hub and pixel canvas) must remain functionally independent — no workflow in one channel requires action in the other [SKILL:ritroso]
- No subscription or recurring billing in P1 — only one-time purchases [SKILL:ritroso]
- No marketplace or auction mechanics in P1 — fixed pricing only [SKILL:ritroso]
- No community features (comments, follows, DMs) in P1 [SKILL:ritroso]
- No native mobile application in P1 — web-only [SKILL:ritroso]
- No third-party API access in P1 [SKILL:ritroso]

## Hard limits — technical
- Every database query must use parameterized statements — no raw string concatenation for SQL queries [SKILL:code-quality]
- Payment webhook handlers must be idempotent — duplicate webhooks must not create duplicate orders [SKILL:security-defensive]
- Canvas block reservation must use database-level locking or unique constraints — double-booking is impossible at the data layer [SKILL:security-defensive]
- Canvas grid must render using canvas element or WebGL, not individual DOM elements per block [SKILL:code-quality]
- All user-generated content (titles, descriptions, canvas text) must be sanitized before storage [SKILL:security-defensive]
- Canvas image uploads must be validated: image/* MIME type only, maximum 5MB [SKILL:security-defensive]

## Hard limits — injected from active skills

### code-quality limits [SKILL:code-quality]
- All server actions must have explicit try/catch with user-facing error messages — no silent failures [SKILL:code-quality]
- No hardcoded values for pricing, block sizes, or zone multipliers — all must be configurable constants [SKILL:code-quality]
- Canvas rendering component must be ≤300 lines — split into sub-components if exceeded [SKILL:code-quality]

### security-defensive limits [SKILL:security-defensive]
- Stripe secret key must never appear in client-side code, environment variables exposed to browser, or version control [SKILL:security-defensive]
- Admin moderation actions must be logged with timestamp, actor identity, and target entry ID — no unlogged moderation [SKILL:security-defensive]
- All user sessions must use HTTP-only cookies with SameSite=Strict [SKILL:security-defensive]

## Forbidden patterns (technical)
- String interpolation in SQL queries — injection vulnerability [SKILL:code-quality]
- Client-side payment verification (no server webhook) — users can bypass payment [SKILL:security-defensive]
- Rendering canvas as a table of div elements — performance will collapse at 100+ blocks [SKILL:code-quality]
- Auto-publishing changelog entries without any moderation step — spam will destroy platform credibility [SKILL:security-defensive]

## Forbidden patterns (product)
- Forcing a user to create a changelog entry to use the canvas, or vice versa — violates the independence guarantee [SKILL:ritroso]
- Hiding the existence of one channel behind the other in the UI — users must discover both channels independently [SKILL:ritroso]
- Making canvas blocks permanently owned with no possibility of transfer or expiration — creates lock-in and reduces liquidity [SKILL:ritroso]

## Regulatory / legal
- GDPR applies — EU users have the right to data deletion, portability, and consent management [SKILL:security-defensive]
- Stripe handles PCI compliance — uptapix.bid never touches raw card data [SKILL:security-defensive]
- Canvas content may contain user-generated material — a DMCA takedown process must be available [SKILL:security-defensive]
- Cookie consent: Plausible Analytics is GDPR-compliant and does not use tracking cookies — no consent banner required [SKILL:security-defensive]

## Consistency check
No contradictions detected between 08_LIMITS and 01_GOAL / 02_PRODUCT. The independence limit directly supports the core promise in 01_GOAL. The technical limits support the failure recovery paths in 10_ERROR. [SKILL:ritroso]
