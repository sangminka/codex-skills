---
name: commit-message-convention
description: Use when writing, reviewing, or suggesting git commit messages, commit subjects, git commit -m text, or Conventional Commits for QT-AI project changes
---

# Commit Message Convention

## Overview

Generate Korean commit messages that follow the team's Conventional Commits subset and keep each commit scoped to one logical change.

## Workflow

1. Inspect the actual change before writing a message.
   - Prefer staged changes: `git status --short`, `git diff --cached --stat`, `git diff --cached --name-only`.
   - If nothing is staged, say so and base the message on unstaged changes only when the user clearly wants that.
2. Choose exactly one type from the allowed list.
3. Choose one scope from the main changed domain, module, or document area.
4. Write `{type}({scope}): {subject}`.
5. Add a body only when it materially clarifies multi-file or policy-heavy changes.

## Format

```text
{type}({scope}): {subject}
```

Allowed types:

| type | Use for |
| --- | --- |
| `feat` | New product or backend behavior |
| `fix` | Bug fix |
| `docs` | Documentation changes |
| `refactor` | Behavior-preserving code restructuring |
| `test` | Test additions or test changes |
| `chore` | Build, dependency, CI, tooling, or maintenance |

Subject rules:

- Write the subject in Korean by default.
- Keep `type` and `scope` in ASCII English, but write the text after `: ` in natural Korean.
- Do not end with a period.
- Do not use vague subjects such as "modify", "fix bug", "wip", or "work in progress".
- Do not claim a broader change than the diff proves.
- If the user explicitly asks for English, only then write the subject in English.

## Scope Selection

Prefer domain or module scopes already used by the project:

```text
member, qt, bible, ai, simulator, requirements
```

Use a document or tooling scope when appropriate:

```text
docs(requirements): align v3.1 decisions
chore(ci): add quality gate workflow
```

If a staged change mixes unrelated domains, recommend splitting commits instead of forcing one broad message. If the user still wants one message, choose the smallest truthful shared scope.

## Good Examples

```text
feat(member): [Korean subject for adding Kakao OAuth login filter]
feat(qt): [Korean subject for adding today QT response aggregator]
feat(bible): [Korean subject for adding 04 KST QT passage collector]
feat(ai): [Korean subject for persisting explanation batch run status]
feat(simulator): [Korean subject for returning simulator availability status]
docs(requirements): [Korean subject for aligning v3.1 decisions]
```

## Bad Examples

```text
modify
fix bug
wip
feat(ai): add user chat SSE
feat(kafka): add event topics
```

The last two are invalid for this project because they describe prohibited product or technology directions.

## Output Style

For a simple request, return one recommended commit message in a fenced `text` block. If there is ambiguity, return a recommended option first and at most two alternatives with a brief reason.

When the diff includes staged files plus unrelated unstaged files, base the recommendation on staged files and mention the unstaged files were not considered.
