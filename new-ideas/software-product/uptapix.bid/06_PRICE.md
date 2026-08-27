---
name: "06_PRICE.md"
description: "Pricing strategy for uptapix.bid — one-time purchases only, no subscriptions. Changelog hub tiers and canvas block pricing based on zone visibility. [ASSUMED-NO-BASIS] items tagged."
---

# 06_PRICE.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Pricing model

uptapix.bid uses a one-time purchase model for both channels. There is no subscription, no recurring billing, and no free tier beyond the landing page. The changelog hub offers three fixed-price tiers based on placement prominence and content format. The canvas charges per block, with block size and zone location determining price. Both channels share a single Stripe account and a single checkout flow when purchased together. The value proposition at each price point is permanent, search-indexable public visibility — something users own outright, unlike social media posts that disappear from feeds.

## Tiers

### Changelog hub
| Tier | Price | What's included | Target user | Revenue assumption |
|------|-------|-----------------|-------------|-------------------|
| Standard | €19 | Title, short description (200 chars), 1 CTA link, standard feed placement | Solo builder, side project, quick update | 60% of changelog purchases |
| Featured | €49 | Title, long description (500 chars), 1 CTA link, image cover, pinned to top for 48h | Startup launch, important announcement | 30% of changelog purchases |
| Launch pack | €99 | Title, full description (1000 chars), 1 CTA link, image cover, pinned for 7 days, priority support | Major product launch, campaign | 10% of changelog purchases |

### Canvas
| Zone | Block size | Price per block | Target user | Revenue assumption |
|------|-----------|-----------------|-------------|-------------------|
| Standard zone | 20×20 pixels | €9 | Casual buyer, meme culture, small brand | 50% of canvas purchases |
| Premium zone | 20×20 pixels | €19 | Brand wanting visibility, creator community | 35% of canvas purchases |
| Header zone | 40×20 pixels | €29 | Brand wanting large presence, event promotion | 15% of canvas purchases |

## Value framing

- **Standard changelog (€19)**: "A builder pays €19 because it gets their product update in front of an audience that discovers new tools daily — something a Twitter post reaches in hours but a changelog entry reaches for weeks."
- **Featured changelog (€49)**: "A startup pays €49 because their launch announcement stays pinned for 48 hours, competing with other launches, and reaches people actively looking for what's new — unlike organic social reach which is declining."
- **Canvas block (€9–€29)**: "A creator pays €9–€29 because they own a permanent pixel of public space on a platform that never deletes it — no algorithm change, no account suspension, no content decay."

## Pricing assumptions

- Users will accept one-time payments rather than subscriptions for this type of product — risk level: medium — reference in 12_ASKED: "Is one-time purchase the right model or should there be a recurring option?"
- €19 is the psychological floor for a paid changelog entry — risk level: low — reference in 12_ASKED: none (supported by similar products)
- Canvas blocks under €9 will attract spam; €9–€19 is the viable range — risk level: medium — reference in 12_ASKED: "What is the minimum viable canvas block price to prevent spam?"

## Revenue projection (P1)

Conservative estimate at end of P1 (12 weeks post-launch):
- Target paying users: 30 (20 changelog + 10 canvas)
- ARPU: €28 (blended across both channels)
- MRR at P1 close: €0 (one-time purchases only; no recurring revenue in P1)
- Total revenue at P1 close: €840
- Break-even at: N/A for P1 — goal is validation, not profitability
