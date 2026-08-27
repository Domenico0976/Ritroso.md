---
name: "12_ASKED.md"
description: "Open questions and assumptions for uptapix.bid. Every [ASSUMED-NO-BASIS] uses the structured 4-field format: claim → files affected → scope impact → entry in 10_ERROR."
---

# 12_ASKED.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Open questions ([INFERRED-FROM-TEXT])
Answer would improve precision but is not blocking.

- [INFERRED-FROM-TEXT] The platform is targetted at Italian/European users (prompt written in Italian, ".bid" TLD suggests international but European context).
  → Currently assumed: primary market is EU/Italian-speaking makers and creators
  → Files affected: 01_GOAL, 06_PRICE, 07_BUDGET
  → Impact if wrong: medium — pricing in EUR is appropriate for EU; if US-focused, USD pricing and different payment preferences would be needed

- [INFERRED-FROM-TEXT] The founder has full-stack development capability (no team mentioned, solo builder context).
  → Currently assumed: single person handles product, dev, moderation, and growth
  → Files affected: 03_NEXT_STEPS, 07_BUDGET, 09_AGENTS
  → Impact if wrong: high — if the founder needs to hire help, budget increases by €3,000–5,000 and timeline extends by 4–6 weeks

- [INFERRED-FROM-TEXT] The canvas is a static grid (not real-time multiplayer).
  → Currently assumed: blocks are purchased, rendered, and updated asynchronously; no live cursor or collaborative editing
  → Files affected: 02_PRODUCT, 05_COMPONENTS, 08_LIMITS
  → Impact if wrong: high — real-time collaboration would require WebSocket infrastructure, changing the entire tech stack and budget

## Assumptions without basis ([ASSUMED-NO-BASIS])
Answer would change the output. Structured 4-field format required.

- [ASSUMED-NO-BASIS] Both channels (changelog hub and pixel canvas) will have comparable market demand.
  → Files affected: 01_GOAL, 02_PRODUCT, 06_PRICE, 07_BUDGET, 03_NEXT_STEPS
  → Scope impact: high
  → Entry in 10_ERROR: Failure 5 — One channel dominates and the other becomes irrelevant

- [ASSUMED-NO-BASIS] The canvas blocks are permanent ownership (no expiration, no rent periods).
  → Files affected: 02_PRODUCT, 05_COMPONENTS, 06_PRICE, 07_BUDGET
  → Scope impact: medium
  → Entry in 10_ERROR: Failure 2 — Double-booking of canvas blocks (permanence affects the economic model: if blocks never expire, scarcity is fixed and secondary market dynamics emerge)

- [ASSUMED-NO-BASIS] Changelog entries require manual moderation before publishing (not auto-approved).
  → Files affected: 03_NEXT_STEPS, 05_COMPONENTS, 09_AGENTS
  → Scope impact: medium
  → Entry in 10_ERROR: Failure 4 — Spam or inappropriate content published on the changelog hub

- [ASSUMED-NO-BASIS] The platform operates under EU jurisdiction (GDPR applies).
  → Files affected: 05_COMPONENTS, 08_LIMITS
  → Scope impact: low
  → Entry in 10_ERROR: none (regulatory compliance is a baseline requirement regardless of jurisdiction)

## Dependency warning
No P1 step in 03_NEXT_STEPS depends on an unresolved ASSUMED-NO-BASIS item. The most critical assumption (comparable demand for both channels) is addressed through the P1 phased approach: changelog hub is built first (Phase 2), canvas second (Phase 3). If changelog validates but canvas does not, the founder can pivot canvas to P2 or sunset it without losing the changelog investment.

## Decisions deferred from this generation
| Decision | Deferred to | Reason | Blocking? |
|----------|-------------|--------|-----------|
| Exact canvas grid dimensions (e.g. 100×100, 200×200) | Phase 3.1 (canvas schema design) | Depends on performance testing with real browser | No |
| Whether to include a free trial or sample block on the canvas | Phase 1 (landing validation) | Unknown until user feedback is collected | No |
| Brand name, logo, and visual identity | Phase 4 (polish & launch) | Not needed for MVP validation | No |
| Whether to add a referral or affiliate program | P2 or later | Not relevant for initial validation | No |
