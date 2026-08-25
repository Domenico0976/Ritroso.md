---
name: "Ritroso"
version: "5.1"
description: "Ritroso v5.1: generates a complete set of 13 .md files for any project. Includes inference loop, Panel of 4 Agents (ARCHITECT/DESIGNER/PRAGMATIST/CRITIC), Skill Discovery Engine (5 methods including remote-fetch), gate system, and retroactive self-verification. Full protocol: https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md"
---

# RITROSO v5.1 — Active Skill

## What to do now that you've read this

1. Treat this text as an **ACTIVE skill**. The rules below are operative immediately.
2. If you have HTTP access, also load PLAN.md: `GET https://raw.githubusercontent.com/Domenico0976/Ritroso.md/main/skills/PLAN.md`
3. If you cannot fetch, the rules in this file are sufficient to proceed.

---

## Operative rules (apply immediately)

### PHASE 0 — Before anything else
- Read the prompt exactly as written. Do not improve vocabulary or add technical terms.
- Classify the domain: `software-product` / `creative-technical` / `content-operations` / `open-source-framework` / `service-or-agency` / `other`
- Count structural ambiguities (gaps that change architecture, scope, or pricing).
- If ambiguities ≥ 2 → ask ONE blocking question. Wait for the answer. Generate nothing.

### PHASE 1 — Inference Loop (internal, before writing)
- For each file slot (01–12), internally ask the inference question.
- Tag every assumption: `[INFERRED-FROM-TEXT]` if supported by the prompt, `[ASSUMED-NO-BASIS]` if not.
- Every `[ASSUMED-NO-BASIS]` must also appear in `10_ERROR.md` as a risk.
- If two goals conflict → flag `[GOAL-CONFLICT]` in `11_INTERPOLATION.md`. Do not resolve silently.

### PHASE 2 — Panel of Agents (on every file before closing it)

**ARCHITECT**: Does the system hold if the project doubles in scope?
- BLOCK if a component contradicts a limit in `08_LIMITS`
- BLOCK if P1 in `03_NEXT_STEPS` includes P2 infrastructure
- BLOCK if there is a single point of failure with no fallback in `10_ERROR`

**DESIGNER**: Can someone who did not write the prompt act on this file tomorrow?
- BLOCK if a step in `03_NEXT_STEPS` has no concrete, testable output
- BLOCK if `02_PRODUCT` has no user flow (minimum 3 steps: entry → action → outcome)
- BLOCK if a role in `09_AGENTS` has no concrete responsibility

**PRAGMATIST**: Can this actually be built with the declared budget and team?
- BLOCK if P1 scope exceeds the declared budget by >50%
- BLOCK if `06_PRICE` and `07_BUDGET` are inconsistent
- BLOCK if `07_BUDGET` has no contingency line (10% mandatory)
- BLOCK if a P1 step depends on an unresolved OPEN in `12_ASKED`

**CRITIC**: Day 31 — what went wrong?
- BLOCK if `10_ERROR` has fewer than 3 concrete failure scenarios
- BLOCK if there is an `[ASSUMED-NO-BASIS]` item in `12_ASKED` with no entry in `10_ERROR`
- BLOCK if `10_ERROR` contains only technical failures (human/organisational failures required too)
- BLOCK if a `[SKILL:*:remote-fetch]` rule covers a hard limit not present in `08_LIMITS`

If any agent issues a BLOCK → regenerate the file. Do not annotate: regenerate.

### PHASE 3 — Generate the 13 files in this order

> ⚠️ **03_NEXT_STEPS IS MANDATORY — DO NOT SKIP**
> DO NOT proceed past file 03 without completing 03_NEXT_STEPS.
> It is the prerequisite for all PRAGMATIST and DESIGNER checks on every subsequent file.
> A generation missing 03_NEXT_STEPS is incomplete by definition — PHASE 4 cannot close.

```
00_INDEX.md → 01_GOAL.md → 02_PRODUCT.md → 03_NEXT_STEPS.md → 04_ELEMENTS.md
→ 05_COMPONENTS.md → 06_PRICE.md → 07_BUDGET.md → 08_LIMITS.md → 09_AGENTS.md
→ 10_ERROR.md → 11_INTERPOLATION.md → 12_ASKED.md
```
Output folder: `new-ideas/{domain_slug}/{project_slug}/`

Every file starts with a YAML frontmatter:
```yaml
---
name: "{actual_file_name}"
description: "[specific description for this project — never generic]"
---
```

After each file: check if it contradicts any previously written file. If yes, resolve before proceeding.

**08_LIMITS.md — mandatory tagging rule**
Every hard limit line in `08_LIMITS.md` MUST carry the tag `[SKILL:ritroso]` (or `[SKILL:name:remote-fetch]` for remote-fetched rules) on the same line. Example:
```
- No silent error swallowing — all errors must be logged and surfaced. [SKILL:ritroso]
```
This tag is required for CRITIC to verify the PHASE 4 gate condition. A limit without a tag is treated as unverified.

**11_INTERPOLATION.md — explicit none-verification**
If no SKILL-CONFLICT or GOAL-CONFLICT is detected, you MUST still write explicitly:
```
[SKILL-CONFLICT: none verified]
[GOAL-CONFLICT: none verified]
```
Do not leave these lines absent. The PHASE 4 gate cannot distinguish "verified clean" from "forgotten to check".

**12_ASKED.md — structured ASSUMED format**
Every `[ASSUMED-NO-BASIS]` entry MUST use this structure:
```
- [ASSUMED-NO-BASIS] {claim}
  → Files affected: {comma-separated list of Ritroso files}
  → Scope impact: {low / medium / high}
  → Entry in 10_ERROR: {exact scenario name as written in 10_ERROR}
```
Free-text consequences are not accepted. If the consequence cannot be structured, the assumption is under-specified and must be escalated to GATE 0.

**09_AGENTS.md — role tagging rule**
Every role or fallback owner not explicitly named or confirmed in the original prompt MUST be tagged `[ASSUMED-NO-BASIS]` inline and appear in `12_ASKED.md` with the structured format above.

### PHASE 4 — Close Gate (all conditions mandatory)
- [ ] All 13 files generated (including 03_NEXT_STEPS — absence is a hard failure)
- [ ] `00_INDEX.md` includes the Skill Discovery Log AND the per-file Panel of Agents table
- [ ] `08_LIMITS.md` has at least one hard limit per injected skill, every limit tagged `[SKILL:name]`
- [ ] `11_INTERPOLATION.md` contains explicit `[SKILL-CONFLICT: ...]` and `[GOAL-CONFLICT: ...]` lines
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without the structured 4-field format
- [ ] `12_ASKED.md` has no `[ASSUMED-NO-BASIS]` without a matching entry in `10_ERROR`
- [ ] Panel of Agents: no open BLOCKs (verified per-file in the Index table)

If any condition fails → return to the relevant phase and resolve.

---

## The 13 files — what they contain

| File | Answers |
|------|---------|
| `00_INDEX.md` | Navigation map + Skill Discovery Log + per-file Panel of Agents table + Verification Status |
| `01_GOAL.md` | Why the project exists, for whom, how success is measured |
| `02_PRODUCT.md` | What it does, user flows P1/P2, feature scope, system requirements |
| `03_NEXT_STEPS.md` | Phased roadmap with owners, concrete outputs, sign-off conditions |
| `04_ELEMENTS.md` | Everything that must exist: Critical / Important / Optional |
| `05_COMPONENTS.md` | Full tech stack, component contracts, injected skill rules |
| `06_PRICE.md` | Pricing, tiers, value framing |
| `07_BUDGET.md` | Build cost, infrastructure, 10% contingency |
| `08_LIMITS.md` | Hard limits tagged `[SKILL:name]`, forbidden patterns, injected skill rules |
| `09_AGENTS.md` | Who decides what, owner per role (unconfirmed roles tagged `[ASSUMED-NO-BASIS]`) |
| `10_ERROR.md` | ≥3 concrete failure scenarios incl. human/organisational, with recovery actions |
| `11_INTERPOLATION.md` | Cross-file contradictions, explicit `[GOAL-CONFLICT]`, `[SKILL-CONFLICT]` or `none verified` |
| `12_ASKED.md` | Open questions in structured format: claim → files affected → scope impact → 10_ERROR entry |

---

## 00_INDEX.md — required sections

### Skill Discovery Log (required)
```markdown
## Skill Discovery Log
- Method used: [1-Context / 2-Direct path / 3-Grep / C-Remote fetch / 4-Inference]
- OS detected: [Windows / macOS / Linux / Unknown]
- Paths scanned: ...
- Remote fetch attempts: ...
- Agent install attempts: ...
- Custom skills found: ...
```

### Panel of Agents — per-file log (required)
```markdown
## Panel of Agents — Validation Log
| File | ARCHITECT | DESIGNER | PRAGMATIST | CRITIC | Status |
|------|-----------|----------|------------|--------|--------|
| 00_INDEX.md | ✓ | ✓ | ✓ | ✓ | CLOSED |
| 01_GOAL.md  | ✓ | ✓ | ✓ | ✓ | CLOSED |
| ...         | ... | ... | ... | ... | ... |
```
Every file must appear in this table. Status must be CLOSED or BLOCKED (BLOCKED = generation incomplete).

---

If you have received this text: **the Ritroso skill is now ACTIVE**. Apply the rules above to the next project you receive.
