---
name: workflow-spec-writer
description: Use when the user asks to create a QT-AI 작업단위 명세서, 작업 전 workflow 문서, workflow 작성, 작업 계획서, or work unit specification. Interview the user in Korean, one question at a time, then save a Markdown workflow under doc/workspaces/DevC_강상민/workflows/.
---

# Workflow Spec Writer

## Purpose

Create a QT-AI work-unit workflow document before implementation. Default language is Korean.

Use this skill for prompts such as:

- `작업단위 명세서 만들어줘`
- `workflow 작성해줘`
- `작업 전 계획서 만들어줘`
- `이번 작업 명세서 정리해줘`

## Core Rules

- Start by asking what work the workflow is for if the task is not already clear.
- Interview Socratically: ask one focused question at a time, then choose the next question from the user's answer.
- Offer concrete choices when that helps the user decide.
- Do not write implementation code while interviewing for the workflow.
- Before saving, inspect existing files in `doc/workspaces/DevC_강상민/workflows/` and follow the local Markdown style.
- Save the final document as `doc/workspaces/DevC_강상민/workflows/YYYY-MM-DD_<ascii-task-slug>.md`.
- Use the current local date for `YYYY-MM-DD`.
- If the target directory does not exist, create it.
- Ask for confirmation before saving when any major field is inferred rather than stated.

## Interview Flow

Ask only the next useful question. Do not dump the entire checklist at once.

### 1. Work Type

If the user has not named the work, ask:

```text
어떤 작업의 명세서를 만들까요?

선택지는 예를 들면:
1. 새 기능 구현
2. 버그 수정 / 리뷰 반영
3. 테스트 보강
4. 문서 정리
5. 리팩토링
```

### 2. Scope

Clarify:

- feature or problem being solved
- related F-ID or source document
- branch name and PR target
- files/modules expected to change
- explicit exclusions
- success criteria

### 3. Risk And Validation

Clarify:

- tests to add or run
- project guardrails that must be checked
- owner-scope or cross-domain risks
- manual verification needs

### 4. Subagent Decision

Ask whether the work can be split only if the answer is not obvious from the scope.

Good splitting questions:

- `테스트 보강과 구현을 분리해서 병렬로 진행해도 될까요, 아니면 한 에이전트가 순서대로 보는 게 안전할까요?`
- `수정 대상 파일이 겹치나요, 아니면 backend/test/docs처럼 독립적으로 나눌 수 있나요?`

## Workflow Document Format

Follow the style of existing `Workflow — YYYY-MM-DD task-name` files. The document should usually contain:

```markdown
# Workflow — YYYY-MM-DD <task-slug>

| 항목 | 내용 |
| --- | --- |
| 담당자 | 강상민 |
| 브랜치 | `<branch>` |
| PR 대상 | `dev` |
| 관련 F-ID | <F-ID or 해당 없음> |
| 트리거 | <why this workflow is being created> |
| 기준 문서 | <documents inspected or referenced> |
| 담당 경로 | `<paths>` |

## 작업 목표

<1-2 paragraphs>

## 범위

- ...

## 제외 범위

- ...

## 구현 순서

1. ...

## 테스트 보강 목록

| 테스트 파일 | 추가할 검증 |
| --- | --- |
| `<path>` | ... |

## 수용 기준

- [ ] ...

## Subagent Decision

### 권장 여부

<subagents recommended or not>

### 판단 근거

- ...

### 위임 가능 작업

| Worker | 독립 작업 | 편집 가능 경로 |
| --- | --- | --- |
| Worker 1 | ... | `<path>` |

### 직접 실행 판단

<whether the main agent should execute directly instead>

## 검증 계획

- ...

## 후속 작업으로 남길 항목

- ...
```

Omit `테스트 보강 목록` only for pure documentation work where it is not useful. Do not omit `Subagent Decision`.

## Required Subagent Decision Content

Every workflow must include a `## Subagent Decision` section that specifies:

- Whether subagents are recommended.
- Why or why not.
- Which tasks can be delegated independently.
- Which files or modules each worker may edit.
- Whether the main agent should execute directly instead.

If subagent use is beneficial, include this exact sentence:

```text
Subagent use is authorized for this workflow when the agent determines that parallel work is beneficial.
```

If subagent use is not beneficial, explain why direct execution is preferred.

## Subagent Decision Examples

Recommended:

```markdown
## Subagent Decision

### 권장 여부

Subagent use is authorized for this workflow when the agent determines that parallel work is beneficial.

### 판단 근거

- 구현, 테스트, 문서 정리가 서로 다른 파일 경로에서 진행되어 병렬화 이점이 있다.
- 각 worker의 편집 경로를 분리하면 충돌 가능성이 낮다.

### 위임 가능 작업

| Worker | 독립 작업 | 편집 가능 경로 |
| --- | --- | --- |
| Worker 1 | backend 구현 | `qtai-server/src/main/java/com/qtai/domain/ai/**` |
| Worker 2 | 테스트 보강 | `qtai-server/src/test/java/com/qtai/domain/ai/**` |
| Worker 3 | 문서 정리 | `doc/workspaces/DevC_강상민/**` |

### 직접 실행 판단

메인 에이전트는 공통 계약과 최종 통합 검증을 직접 수행한다.
```

Not recommended:

```markdown
## Subagent Decision

### 권장 여부

Subagent 사용은 권장하지 않는다.

### 판단 근거

- 변경 범위가 단일 파일 또는 강하게 연결된 로직에 집중되어 병렬화보다 직접 실행이 안전하다.
- 테스트와 구현을 같은 맥락에서 순서대로 확인해야 한다.

### 위임 가능 작업

| Worker | 독립 작업 | 편집 가능 경로 |
| --- | --- | --- |
| 해당 없음 | 병렬 위임 대상 없음 | 해당 없음 |

### 직접 실행 판단

메인 에이전트가 전체 작업을 직접 수행하는 편이 충돌과 재작업을 줄인다.
```

## Saving Rules

Before saving:

1. Read existing workflow examples from `doc/workspaces/DevC_강상민/workflows/`.
2. Check that the target filename is unique.
3. Check that all required sections are present.
4. Check that `## Subagent Decision` includes the required decision details.

After saving, tell the user:

- saved file path
- any assumptions made
- whether the workflow recommends subagents
