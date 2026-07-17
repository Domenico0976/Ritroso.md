# RITROSO — Contextual Reasoning Skill v3

> Contextual reasoning skill for LLMs with multimodal generation
> of complete `.md` file sets, inference loop, and retroactive self-verification.

**When you receive a prompt, execute these steps in order:**

1. Classify the request type (domain)
2. Create a dedicated folder `new-ideas/{domain_slug}/{project_name_slug}/`
3. Run the **context gap protocol** — if context is empty or insufficient, ask questions
4. Run the **inference loop** — files interrogate each other before generation
5. Generate a **complete set of 13 `.md` files** for that project
6. For every generated file, add a YAML frontmatter block at the top with:
   - `name`: exact real file name
   - `description`: dynamic, context-aware operative interpretation for the agent reading that file
7. Self-verify the entire set with the negative prompt before delivering output

The system is **multimodal**: reads text, code, images, PDFs, video,
GitHub repos and any resource available in context.

---

## Dynamic File Frontmatter Rule

Every generated file in the final output folder MUST begin with:

```yaml
---
name: "{real_file_name}"
description: "Dynamic agent-facing description built from the file objective, current project context, inferred limits, declared constraints, anti-patterns, and intended outcome. It must explain how to read the file, what reasoning approach to prioritize, what limits matter most, and what to do if context is incomplete."
---
```

### Frontmatter Constraints

- `name` must exactly match the file name
- `description` must never be generic when context exists
- `description` must mention the file objective
- `description` should include inferred or explicit limits when relevant
- `description` should guide the consuming agent's reasoning approach
- if context is weak, description must instruct the agent to rely on linked files and unresolved questions rather than hallucinating

---

## File Generation Rule Extension

During file generation, after the standard header is resolved:

1. Compute the file objective
2. Collect the most relevant constraints from:
   - prompt
   - context-gap answers
   - inference loop
   - linked files
3. Build a dynamic `description`
4. Place it in YAML frontmatter above the markdown title
5. Re-check in negative verification that the description is coherent with the actual file content
