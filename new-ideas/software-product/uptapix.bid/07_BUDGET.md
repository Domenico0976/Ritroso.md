---
name: "07_BUDGET.md"
description: "Budget estimate for uptapix.bid P1 MVP — solo founder dev time, infrastructure costs, and contingency. Consistency check against 06_PRICE revenue projection included."
---

# 07_BUDGET.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Build cost — P1
| Item | Type | Cost | Frequency | Notes |
|------|------|------|-----------|-------|
| Founder dev time (Phases 2–3, ~120 hours) | Labour | €0 | one-time | Solo founder; cost is time, not money |
| Domain registration (uptapix.bid) | Fixed | €12 | yearly | .bid TLD registration |
| Vercel hosting (pro tier for production) | Fixed | €20 | monthly | Hobby tier covers P1; upgrade if traffic grows |
| Neon PostgreSQL (free tier) | Fixed | €0 | monthly | Free tier sufficient for P1; upgrade if needed |
| Supabase Auth (free tier) | Fixed | €0 | monthly | Free tier includes 50k MAU |
| Supabase Storage (free tier) | Fixed | €0 | monthly | Free tier includes 1GB storage |
| Stripe (standard fees) | Variable | ~€0.30 + 2.9% | per transaction | Paid by customer; no upfront cost |
| Sentry (free tier) | Fixed | €0 | monthly | Free tier includes 5k errors/month |
| Plausible Analytics (privacy tier) | Fixed | €0 | monthly | Free tier includes 5k pageviews |
| **Subtotal P1** | | **€32** | | Infrastructure cost only |
| **Contingency (10%)** | | **€3** | | mandatory |
| **TOTAL P1** | | **€35** | | |

## Build cost — P2 (estimate)
| Item | Cost estimate | Confidence |
|------|--------------|------------|
| Vercel pro upgrade (if traffic grows) | €20/month | high |
| Additional storage for canvas images | €10/month | medium |
| Possible hired help (UI polish, 20 hours) | €400 | low |
| **TOTAL P2 estimate** | **€430** | |

## Infrastructure (recurring post-launch)
| Service | Cost/mo | Scales with | Limit before upgrade |
|---------|---------|-------------|----------------------|
| Vercel | €0–20 | requests | >100k builds/month or custom domain needed |
| Neon PostgreSQL | €0 | rows + storage | >1GB data or 1M rows |
| Supabase Storage | €0 | uploads | >1GB total storage |
| Plausible | €0 | pageviews | >5k monthly pageviews |

## Budget assumptions
- Founder dev time is unpaid — if a contractor were hired for the same work, P1 cost would be €3,000–5,000 → impact: €3,000 delta on P1
- Contingency covers: unexpected Vercel upgrades, additional domain-related costs, Sentry overage if errors spike during launch
- Infrastructure stays on free tiers through P1; upgrade only if usage triggers it

## Budget vs Price consistency check
- Total build cost (founder time excluded): €35
- Total build cost (founder time included at €25/hour): €3,035
- Break-even at MRR: N/A (one-time purchases; total revenue projection €840 at P1 close)
- Flag if inconsistent with 06_PRICE: NO — P1 is a validation phase, not a profitability phase. Revenue covers infrastructure; the real investment is founder time.
