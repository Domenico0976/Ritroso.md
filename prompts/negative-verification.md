# Negative Self-Verification Prompt — Ritroso.md

> This prompt is injected into the model AFTER generating the complete 13-file set.
> The model must start from the assumption that **it was wrong**.

---

## Instruction to the Model

You just generated a complete project file set. **Start from the assumption that it is wrong.**

Do not defend what you wrote — dismantle it.
Only if you can dismantle it and find no serious errors, you may consider it valid.

---

## Pass/Fail Criteria (explicit — not interpretable)

For each check below: assign **PASS**, **FAIL**, or **PARTIAL**.
A single **FAIL** on a CRITICAL check requires a [CORRECTION] before the set can be marked [RITROSO-VERIFIED].
A **PARTIAL** on a CRITICAL check requires an [OPEN] note in the affected file + an entry in 12_ASKED.md.

| Check | Type | PASS condition | FAIL condition |
|-------|------|----------------|----------------|
| Every file section is non-empty or has `N/A: [reason]` | CRITICAL | All sections filled | Any section is blank or placeholder |
| No section contains only a generic category (e.g. "a database", "admin user") without a specific name | CRITICAL | All nouns are project-specific | Generic placeholder nouns present |
| 01_GOAL and 02_PRODUCT describe the same project | CRITICAL | Core nouns match | Different product implied |
| Every agent in 09_AGENTS has a corresponding limit in 08_LIMITS | CRITICAL | 1:1 coverage | Unowned limits exist |
| Every failure in 10_ERROR has a corresponding limit in 08_LIMITS | CRITICAL | 1:1 coverage | Uncovered failure modes exist |
| Every [INF] tag traces to a resolved_assumption in the inference loop output | CRITICAL | All [INF] sourced | Unsourced [INF] found |
| Every [OPEN-INF-N] from the loop appears in 12_ASKED.md | CRITICAL | All logged | Missing open question |
| Budget in 07_BUDGET can sustain the agents in 09_AGENTS | CRITICAL | Explicit YES or scoped PARTIAL | Silent assumption |
| Next steps in 03_NEXT_STEPS are achievable by the declared team/resources | IMPORTANT | Feasibility confirmed | Steps require undeclared resources |
| Cross_file_signals from inference loop are handled in affected files | IMPORTANT | Each signal addressed | Unaddressed signal |
| 00_INDEX correction count matches actual [CORRECTION] tags across all files | IMPORTANT | Counts equal | Mismatch |
| Inference loop Answer Quality Contract met for all resolved assumptions | IMPORTANT | All pass contract | Any generic resolution accepted |

---

## Negative Prompt (to inject with the original user prompt)

```
The file set you generated may be incoherent, incomplete or misleading.

Re-read the following original user prompt carefully, without errors and in full:

[ORIGINAL_USER_PROMPT]

Also re-read the inference loop output that preceded generation:

[INFERENCE_LOOP_OUTPUT]

Now run each check in the Pass/Fail Criteria table above.
For each check: write the result (PASS / FAIL / PARTIAL) and one sentence of evidence.
Do NOT skip any check.

1. LOGICAL COHERENCE
   - Does every section of every file answer something explicitly requested or inferable?
   - Did you add assumptions not declared by the user and not tagged [INF]?
   - Is there anything in the files that contradicts the original prompt?
   - Does the written content match what the inference loop predicted?
   - PASS requires: all [INF] tags traceable + no undeclared assumptions

2. CROSS-FILE CONSISTENCY
   - Do all 13 files form a coherent whole?
   - Does what 01_GOAL says match what 02_PRODUCT describes (same project, same vocabulary)?
   - Do the agents in 09_AGENTS have the budget in 07_BUDGET?
   - Do the errors in 10_ERROR have limits in 08_LIMITS to prevent them?
   - Do the next steps in 03_NEXT_STEPS actually lead toward the goal in 01_GOAL?
   - PASS requires: explicit cross-reference in each of the above pairs

3. REAL IMPACT
   - If this project were in production tomorrow, what would fail FIRST?
   - Name it. Do not say "many things could fail" — name ONE thing.
   - Is it covered by 10_ERROR + 08_LIMITS?
   - PASS requires: named failure + confirmed coverage

4. SPECIFICITY CHECK
   - Read every placeholder-style noun: "a service", "the user", "the system", "a component"
   - Each one is a specificity failure unless it refers to a named entity defined elsewhere in the set
   - PASS requires: zero unnamed generic nouns in critical sections (GOAL, PRODUCT, LIMITS, AGENTS)

5. USER'S LIFE
   - What you produced has a concrete impact on the life or work of whoever made the request.
   - Did you treat this seriously?
   - Is there a risk you minimized that is actually critical for THIS user?
   - Is there a complexity you added that THIS user cannot sustain?
   - PASS requires: at least one user-specific adaptation (not generic advice)

6. ANTI-PATTERNS
   - Are you replicating generic solutions instead of responding to the specific context?
   - Are you ignoring constraints the user declared?
   - Are you adding unnecessary complexity?
   - PASS requires: 08_LIMITS anti-patterns are specific to this project, not boilerplate

7. INFERENCE LOOP VALIDATION
   - For each resolved assumption: is it reflected correctly in the files?
   - For each [OPEN-INF-N]: is it present in 12_ASKED.md with reason_unresolved?
   - For each cross_file_signal: is it handled in ALL affected files?
   - PASS requires: 100% traceability in both directions

8. OPEN QUESTIONS
   - Are [OPEN] tags justified — not used as an excuse to avoid deciding?
   - Does 12_ASKED.md contain the single most impactful unanswered question?
   - Are there critical decisions deferred without declaration?
   - PASS requires: every [OPEN] has a reason + every critical deferral is declared

After this analysis:
- For every FAIL → apply [CORRECTION] inline in the affected file and log in 00_INDEX
- For every PARTIAL on a CRITICAL check → add [OPEN] in the file + entry in 12_ASKED.md
- If all CRITICAL checks PASS → write [RITROSO-VERIFIED] and list the evidence for each
- If any CRITICAL check FAILS → the set CANNOT be marked [RITROSO-VERIFIED] until fixed

Remember: "seems coherent" is not a PASS. Name the specific evidence.
```

---

## Usage Notes

- `[ORIGINAL_USER_PROMPT]` is replaced with the content of `.ritroso_prompt_cache.tmp`
- `[INFERENCE_LOOP_OUTPUT]` is replaced with the output from `prompts/inference-loop.md`
- This prompt is NOT optional — it is a mandatory part of the Ritroso.md flow
- The model cannot skip this step even if it is "confident" in its output
- Corrections applied are logged in 00_INDEX with prefix `[CORRECTION]`
- A set with uncorrected CRITICAL failures must be flagged `NEEDS_REVIEW` in 00_INDEX
- The inference loop output is also verified: if the written files contradict the loop, it is a CRITICAL failure
