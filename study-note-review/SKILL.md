---
name: study-note-review
description: Review beginner study notes, commented code, and YAML against the user's AI TOOL Notion pages, check for overlapping coverage first, point out missing concepts, and recommend whether no addition is needed, an existing page should be updated, or a new page should be created.
---

# Study Note Review

Use this skill when the user shares study notes, annotated code, commented YAML, or partial explanations and wants:

- feedback on whether the explanation is correct enough for their current learning stage
- a check for whether the topic is already sufficiently covered in `AI TOOL`
- missing points compared with their existing `AI TOOL` Notion pages
- a recommendation on whether no addition is needed, an existing page should be updated, or a new page should be created
- follow-up Notion work after they choose the direction

## Core Rules

- Treat the material as a **concept-learning project**, not a production architecture review, unless the user explicitly asks for project analysis.
- Evaluate **only the code, config, or explanation that is currently provided**.
- Do not criticize the absence of files, layers, or later-stage infrastructure that the user has not shared yet.
- Code may be incomplete because the user is learning step by step. Prefer "currently not shown" over "missing implementation" unless the visible content is actually wrong.
- Keep explanations beginner-friendly and in Korean unless the user asks otherwise.

## Review Workflow

1. Fetch the `AI TOOL` parent page:
   - `https://www.notion.so/33350192129180579cefc16fa7f63bf8`
2. Identify only the child pages relevant to the note the user shared.
3. Compare the user's current explanation with those existing pages and check whether the topic is already sufficiently covered.
4. Answer in this order:
   - what the user already understood correctly
   - what concept is still missing or could be stated more clearly
   - whether the content is already covered enough that no Notion change is needed
   - if new documentation is still useful, whether this belongs in a new page or an existing page
   - if existing page is better, name the specific page and where the content should be added
5. Do **not** write to Notion yet if the user is still deciding where it should go.
6. After the user chooses, create or update the Notion page as requested.

## Recommendation Rules

Choose **no addition needed** when:

- the same concept is already explained clearly enough in an existing `AI TOOL` page
- the user's note is mostly repeating what is already documented
- adding it again would create duplication without improving understanding

Choose **existing page update** when:

- the new note is a refinement of an existing concept already documented in `AI TOOL`
- the content is a deeper explanation of the same Docker, Compose, NGINX, Linux, or MSA topic
- splitting it into a separate page would make the notes harder to navigate

Choose **new page** when:

- the topic has a clearly different learning goal from existing pages
- the content would make an existing page too long or too mixed
- the note becomes a reusable standalone reference

When recommending an existing page, be specific. Mention:

- the exact page title
- the best insertion point, such as "after concept section" or "before summary"

When recommending **no addition needed**, mention:

- which existing page already covers it
- whether the overlap is complete or only missing a very small detail

## What to Emphasize in Feedback

- Prefer concept clarity over completeness.
- Point out terminology corrections when they would prevent future confusion.
- If the user's wording is broadly correct for a beginner, say that first before adding refinements.
- Separate "wrong" from "not yet covered".

## Good Output Shape

Use a compact structure like this:

1. What is already correct
2. What is missing or should be refined
3. Recommendation:
   - no addition needed vs existing page vs new page
   - if no addition is needed, which page already covers it
   - if existing page is better, which page and where

If the user then asks you to apply the recommendation, proceed with the Notion work.
