# Negative Self-Verification Prompt — Ritroso.md

> This prompt is injected into the model AFTER generating the complete 13-file set.
> The model must start from the assumption that **it was wrong**.

---

## Instruction to the Model

You just generated a complete project file set. **Start from the assumption that it is wrong.**

Do not defend what you wrote — dismantle it.
Only if you can dismantle it and find no serious errors, you may consider it valid.

---

## Negative Prompt (to inject with the original user prompt)

```
The file set you generated may be incoherent, incomplete or misleading.

Re-read the following original user prompt carefully, without errors and in full:

[ORIGINAL_USER_PROMPT]

Also re-read the inference loop output that preceded generation:

[INFERENCE_LOOP_OUTPUT]

Now analyze the entire 13-file set from these angles:

1. LOGICAL COHERENCE
   - Does every section of every file answer something explicitly requested?
   - Did you add assumptions not declared by the user?
   - Is there anything in the files that contradicts the original prompt?
   - Does the written content match what the inference loop predicted?

2. CROSS-FILE CONSISTENCY
   - Do all 13 files form a coherent whole?
   - Does what 01_GOAL says match what 02_PRODUCT describes?
   - Do the agents in 09_AGENTS have the budget in 07_BUDGET?
   - Do the errors in 10_ERROR have limits in 08_LIMITS to prevent them?
   - Do the next steps in 03_NEXT_STEPS actually lead toward the goal in 01_GOAL?

3. REAL IMPACT
   - If this project were in production tomorrow, what would fail?
   - Did you consider impact on: TIME, SALES, RESOURCES, DEVELOPMENT?
   - Every choice you made has a cost — did you evaluate it?

4. USER'S LIFE
   - What you produced has a concrete impact on the life or work of
     whoever made the request. Did you treat this seriously?
   - Did you minimize risks that are actually critical?
   - Did you overestimate something that is actually simple?

5. ANTI-PATTERNS
   - Are you replicating generic solutions instead of responding to the specific context?
   - Are you ignoring constraints the user declared?
   - Are you adding unnecessary complexity?

6. INFERENCE LOOP VALIDATION
   - For each resolved assumption in the inference loop: is it reflected correctly in the files?
   - For each [OPEN-INF-N]: is it present in 12_ASKED.md?
   - For each cross_file_signal: is it handled in the affected files?

7. OPEN QUESTIONS
   - Are there sections of the file marked with [OPEN] — are they justified?
   - Are there critical decisions you deferred without saying so?
   - Does 12_ASKED.md contain the most important unanswered question from the inference loop?

After this analysis:
- If you find errors or inconsistencies → CORRECT the file and indicate what changed with [CORRECTION]
- If the file set holds → write [RITROSO-VERIFIED] and briefly explain why

Remember: everything you said must be coherent with the original prompt.
Analyze it from multiple angles. It is not enough that it "makes sense in the abstract" —
it must make sense for THIS project, for THIS user, at THIS moment.
```

---

## Usage Notes

- `[ORIGINAL_USER_PROMPT]` is replaced with the content of `.ritroso_prompt_cache.tmp`
- `[INFERENCE_LOOP_OUTPUT]` is replaced with the output from `prompts/inference-loop.md`
- This prompt is NOT optional — it is a mandatory part of the Ritroso.md flow
- The model cannot skip this step even if it is "confident" in its output
- Corrections applied are logged in the final file with prefix `[CORRECTION]`
- The inference loop output is also verified: if the written files contradict the loop, it is an error
