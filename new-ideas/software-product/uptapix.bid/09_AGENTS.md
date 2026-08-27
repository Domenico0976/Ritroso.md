---
name: "09_AGENTS.md"
description: "Decision owners, roles, handoff protocol, and escalation paths for uptapix.bid. All roles tagged [ASSUMED-NO-BASIS] since the prompt does not specify team structure."
---

# 09_AGENTS.md — uptapix.bid
Domain: software-product · Generated: 2026-08-27 · Ritroso v6.0

---

## Decision matrix
| Decision type | Owner | Fallback owner | Escalation path |
|---------------|-------|----------------|-----------------|
| Product scope changes | Founder [ASSUMED-NO-BASIS] | None | N/A — sole decision maker |
| Pricing adjustments | Founder [ASSUMED-NO-BASIS] | None | N/A |
| Content moderation decisions | Founder [ASSUMED-NO-BASIS] | Community guideline doc | Escalate to 12_ASKED if ambiguous |
| Technical architecture changes | Founder [ASSUMED-NO-BASIS] | None | N/A |
| Launch go/no-go | Founder [ASSUMED-NO-BASIS] | None | N/A |

## Roles

### Founder / Product owner [ASSUMED-NO-BASIS]
- **Responsibilities**: Define product scope, set pricing, approve or reject changelog entries, make all architectural decisions, manage Stripe account and webhook issues
- **Decision authority**: Unilateral on all product, pricing, and technical decisions
- **Blocked by**: None — sole owner
- **Availability**: Part-time (estimated 10–15 hours/week during P1)

### Founder / Builder [ASSUMED-NO-BASIS]
- **Responsibilities**: Implement all code (frontend, backend, database, deployment), write landing page copy, configure Stripe, set up monitoring
- **Decision authority**: Technical implementation choices; can escalate to Product owner role (same person) if stuck
- **Blocked by**: Time availability; technical knowledge gaps
- **Availability**: Part-time; same person as Product owner

### Moderator ops [ASSUMED-NO-BASIS]
- **Responsibilities**: Review changelog submissions in the admin queue; approve clean entries; reject spam or policy-violating content; apply takedowns on canvas content
- **Decision authority**: Approve or reject any changelog entry; takedown any canvas block
- **Blocked by**: Requires access to admin console (granted by Founder)
- **Availability**: On-call; not full-time. Founder covers this role until volume requires delegation.

### Finance / payments reviewer [ASSUMED-NO-BASIS]
- **Responsibilities**: Monitor Stripe webhook failures; investigate payment discrepancies; process refunds if requested; reconcile monthly revenue
- **Decision authority**: Process refunds up to €50 without Founder approval; escalate larger refunds
- **Blocked by**: Stripe dashboard access; requires Founder to grant
- **Availability**: On-call; estimated 2 hours/week maximum

### Growth operator [ASSUMED-NO-BASIS]
- **Responsibilities**: Run landing page A/B tests; post in relevant communities; track conversion metrics; propose pricing experiments
- **Decision authority**: Can run experiments with pre-approved budgets (≤€50); cannot change pricing without Founder
- **Blocked by**: Requires access to analytics dashboard and Stripe test mode
- **Availability**: Part-time; may be the Founder themselves initially

## Handoff protocol
| From | To | Trigger | Required input | Acceptance criteria | Rejection handling |
|------|----|---------|---------------|---------------------|-------------------|
| Founder/Builder | Founder/Product owner | Code complete, ready for review | Deployed staging URL; test case results | Entry works end-to-end in staging | Rejected → bug fix required before re-deploy |
| Founder/Product owner | Moderator ops | Changelog entry in `pending` state | Entry title, description, CTA link, tier | Mod decision within 24h of submission | Rejected entry → user notified with reason |
| Moderator ops | Founder/Product owner | Ambiguous content requiring judgment call | Entry details; moderator's assessment | Founder decision within 48h | N/A — final decision rests with Founder |
| Finance reviewer | Founder/Product owner | Payment discrepancy or refund request >€50 | Transaction ID, amount, issue description | Response within 72h | Escalated to Founder for resolution |

## Escalation paths
| Agent | Blocked/failed → escalates to | Via | Within |
|-------|------------------------------|-----|--------|
| Founder/Builder | Technical blocker (unknown framework issue) | Document in 12_ASKED as [OPEN-INF-N] | 24h |
| Moderator ops | Content ambiguity (policy doesn't cover case) | Escalate to Founder/Product owner | 48h |
| Finance reviewer | Stripe webhook repeatedly failing | Alert sent to Founder via Sentry + email | 4h |
| Growth operator | Experiment results inconclusive after 7 days | Report to Founder with recommendation | End of experiment |

## Capability assessment
| Agent | Task | Feasible? (YES/PARTIAL/NO) | Constraint | Mitigation |
|-------|------|---------------------------|------------|------------|
| Founder/Builder | Build Next.js app with Stripe + PostgreSQL | YES | Time availability | Scope P1 to essential features only |
| Founder/Product owner | Set pricing and define product scope | YES | None | — |
| Moderator ops | Review changelog entries | PARTIAL | Founder is the first moderator; volume may require delegation | Clear moderation guidelines reduce ambiguity |
| Finance reviewer | Handle payment discrepancies | PARTIAL | Requires Stripe dashboard access; may need external help for complex cases | Start with Founder; bring in accountant if volume grows |
| Growth operator | Run community outreach | YES | Founder may lack marketing experience | Use proven frameworks (Indie Hackers, Twitter/X threads) |

## Automated agents / systems
| System | What it decides autonomously | Human override required for |
|--------|------------------------------|----------------------------|
| Stripe webhook processor | Confirms payment, creates order, updates entry/block status | Webhook signature verification failure |
| Changelog auto-approval | Entries with clean content (no flagged keywords, no Suspicious patterns) are published immediately | Flagged content requires manual review |
| Canvas block reservation lock | Locks selected block for 10 minutes during checkout | Lock expires before payment — block released automatically |

## Communication protocol
- Sync frequency: async only (no standups needed for solo founder)
- Decision log: recorded in 03_NEXT_STEPS.md and 12_ASKED.md as they are generated
- Blocking escalation: any unresolved blocker goes into 12_ASKED.md with [OPEN] tag and priority
