---
name: workflow-spec-runner
description: Use when the user wants to execute a saved QT-AI 작업단위 명세서 or workflow document, especially prompts like "workflow-spec-runner로 실행해줘", "작업단위 명세서 기준으로 실행해줘", "이 workflow 실행해줘". Ask for the workflow spec path when missing, read the spec, route execution through superpowers:executing-plans or superpowers:subagent-driven-development based on Subagent Decision, and always finish with superpowers:verification-before-completion.
---

# Workflow Spec Runner

## Purpose

Run implementation work from a saved QT-AI workflow spec. Default language is Korean.

This skill is an orchestrator. It does not replace the workflow spec. It reads the saved spec, enforces its scope, selects the right execution workflow, and verifies completion before reporting.

## Core Rules

- If the workflow spec path is missing, ask for it first:

```text
실행할 작업단위 명세서 경로를 알려주세요.
예: doc/workspaces/DevC_강상민/workflows/2026-05-20_xxx.md
```

- Do not implement before reading the workflow spec.
- Treat the workflow spec as the source of truth for scope, exclusions, acceptance criteria, and validation.
- Do not edit files outside the spec's scope unless the user explicitly expands scope.
- Respect excluded scope even if related code looks tempting to fix.
- If critical fields are missing or contradictory, stop and ask one focused question.
- If the user asks to create and run a spec in one request, first use `workflow-spec-writer`; after the spec is saved, ask for confirmation before executing if any major field was inferred.
- Always complete with `superpowers:verification-before-completion` or the closest available equivalent if that skill cannot be loaded.

## Execution Flow

### 1. Resolve The Workflow Spec

Use the path provided by the user.

If the user gives only a filename or partial name, search under:

```text
doc/workspaces/DevC_강상민/workflows/
```

If there is exactly one matching file, use it. If there are multiple matches, ask the user to choose. If no file exists, ask for the correct path or whether they want to create a new spec with `workflow-spec-writer`.

### 2. Read And Extract Required Fields

Read the full workflow spec and identify:

- branch and PR target
- related F-ID or source document
- trigger
- owner paths
- work goal
- scope
- excluded scope
- implementation order
- test additions
- acceptance criteria
- `## Subagent Decision`
- validation plan
- follow-up items

If the repository has a Git worktree, inspect current branch and `git status` before editing. Do not revert user changes.

### 3. Confirm Execution Safety

Before modifying files, check:

- Does the requested work fit inside the spec's scope?
- Are excluded items clear enough to avoid accidental edits?
- Are acceptance criteria testable?
- Are owner paths specific enough?
- Are there existing user changes in files that will be edited?
- Is the Subagent Decision complete enough to choose direct or parallel execution?

Ask one focused question if the answer changes the execution path or could cause out-of-scope edits.

## Execution Routing

### Direct Execution

Use `superpowers:executing-plans` when any of these are true:

- `Subagent Decision` says subagent use is not recommended.
- The work is concentrated in one file or tightly coupled module.
- Implementation and tests need to be understood in one sequence.
- Worker edit paths would overlap.
- Parallel work is not explicitly authorized.

In this path, the main agent executes the workflow directly and keeps changes scoped to the spec.

### Parallel Execution

Use `superpowers:subagent-driven-development` only when the workflow spec authorizes it and the work can be split safely.

The strongest authorization signal is this exact sentence in `Subagent Decision`:

```text
Subagent use is authorized for this workflow when the agent determines that parallel work is beneficial.
```

Parallel execution is appropriate when:

- there are two or more independent tasks,
- each worker has a clearly separated edit path,
- implementation, tests, and documentation can progress without shared mutable context,
- the main agent can keep ownership of integration and final verification.

If current tool policy or the user request does not clearly allow subagents, ask before spawning or delegating.

## Required Execution Announcement

After reading the spec and before editing, state the selected path in Korean:

```text
명세서를 확인했습니다. 이번 작업은 <직접 실행 / 병렬 실행>으로 진행하겠습니다.
판단 근거: <1-3 bullets>
```

Then invoke the selected execution skill or follow its equivalent process if the skill is unavailable.

## Verification

Before claiming completion:

1. Use `superpowers:verification-before-completion`.
2. Run the tests or checks named in the workflow spec.
3. If a named check cannot be run, explain why and provide the next best verification.
4. Compare results against every acceptance criterion.
5. Confirm no excluded-scope work was performed.

## Final Report

Report in Korean with:

- workflow spec path used
- execution path chosen: direct or parallel
- changed files
- acceptance criteria status
- tests and verification run
- assumptions or deviations
- follow-up items left from the spec or discovered during execution

Keep the final report concise. Do not include a long replay of the implementation process unless the user asks for it.
