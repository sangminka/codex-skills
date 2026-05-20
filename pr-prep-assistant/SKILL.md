---
name: pr-prep-assistant
description: Use when preparing, reviewing, drafting, or creating QT-AI pull requests, PR titles, PR bodies, PR templates, GitHub PR commands, CI readiness checks, or REQUEST_CHANGES responses
---

# PR Prep Assistant

## Overview

Prepare QT-AI pull requests safely. Default output language is Korean. Default behavior is to inspect the local branch, draft the PR title/body, and propose commands; do not push, open, merge, or hotfix unless the user explicitly asks.

## Operating Boundary

Do by default:

- Inspect branch, status, staged/unstaged changes, and diff summary.
- Check PR size guidance: 10 files or fewer and 500 changed lines or fewer.
- Draft Korean PR title and Korean PR body.
- Identify missing F-IDs, document references, tests, or risk explanations.
- Propose `git push` and `gh pr create` commands without running them.
- Help respond to CI failures or `REQUEST_CHANGES` comments.

Do only after explicit user request:

- Run `git push`.
- Run `gh pr create`.
- Inspect remote PR comments through GitHub tooling.

Never do:

- Push directly to `dev` or `master`.
- Press or automate merge/squash merge.
- Create a PR to `master` except an explicitly requested emergency hotfix.
- Add prohibited temporary code or normalize a known rule violation.
- Modify requirements or owner-scope files without calling out the reason and reviewer need.

## Workflow

1. Inspect repository state.

```powershell
git branch --show-current
git status --short
git diff --stat
git diff --cached --stat
git diff --name-only origin/dev...HEAD
```

If `origin/dev` is stale or missing, ask before fetching unless the user asked to update. Use `dev` as the PR base for normal PRs.

2. Classify the PR.

- `feat`: new behavior. Requires relevant F-ID from `07_요구사항_정의서.md`.
- `fix`: bug fix.
- `refactor`: behavior-preserving restructuring.
- `test`: test additions or changes.
- `docs`: documentation only.
- `chore`: build, dependency, CI, or tooling.

3. Check the risk gates.

Flag critical risks immediately:

- Direct changes to another teammate's owner area without explanation.
- Plain API key, password, token, private key, or secret.
- New independent service directories such as `auth-service`, `bible-service`, or `ai-service`.
- RAG, ChromaDB, vector DB, Elasticsearch.
- Kafka, Kubernetes, Helm v1.
- AI free chatbot, multi-turn chat, SSE, or `/ai/sessions/**`.
- Korean Revised Version, ESV, or NIV seed/response/test data.
- Scripture text from Scripture Union or Duranno.
- Praise lyrics, music files, or AI praise recommendation.
- Church verification screen, button, API, or DB field.

Flag fix-required risks:

- Missing `@Transactional` on DB write path.
- Empty catch block.
- `javax.*` instead of `jakarta.*`.
- Direct imports of another domain's Entity, Service, Repository, or infrastructure type.
- External public API outside `/api/v1/**` except `/oauth2/**`.
- Internal Java interface modeled as HTTP/OpenAPI.
- Simulator state returned as null instead of `READY`, `MISSING`, `FAILED`, or `DISABLED`.
- Today QT response missing passage, explanation entry, note entry, or simulator status.
- Event handler failure without `eventId`, event type, handler name, and error log.
- New feature or core UseCase/Service change without tests.
- Integration test missing without PR body reason.

4. Ask for missing information only when needed.

Ask concise questions for:

- Related F-ID or document references.
- Tests actually run and results.
- Integration test omission reason.
- Why owner-scope files changed.
- Whether push or PR creation should actually run.

5. Draft the PR.

- PR title should follow the commit convention style: `{type}({scope}): Korean subject`.
- PR body must be Korean and use the team template below.
- Fill checkboxes honestly. Do not mark tests as passed unless evidence was provided or commands were run in this session.
- For docs/chore-only PRs, mark the test checklist's docs/chore item.

## PR Body Template

```markdown
## 구현 내용
[이 PR에서 해결하거나 추가하는 변경 사항을 간략히 작성]

## 관련 요구사항 / 문서
[예: F-01-11, F-02-01, 03_아키텍처_정의서.md §5, 04_API_명세서.md §4, 18_코드_품질_게이트.md §3]

## 변경 유형
- [ ] 기능 추가 (feat)
- [ ] 버그 수정 (fix)
- [ ] 리팩토링 (refactor)
- [ ] 테스트 (test)
- [ ] 문서 (docs)
- [ ] 인프라 / CI (chore)

## v3.1 코드·문서 체크리스트
- [ ] `07_요구사항_정의서.md` v3.1과 충돌하지 않음
- [ ] `00_문서_역할_분리표.md`의 문서 책임과 충돌하지 않음
- [ ] 아키텍처 변경은 `03_아키텍처_정의서.md`, API 변경은 `04_API_명세서.md`, 품질 게이트 변경은 `18_코드_품질_게이트.md`, 용어 변경은 `23_도메인_용어사전.md`와 정합함
- [ ] 단일 `qtai-server` Modular Monolith 기준을 지킴
- [ ] `note`, `sharing`, `praise`는 별도 도메인으로 유지하고 독립 서비스로 분리하지 않음
- [ ] 다른 도메인의 Entity/Service/Repository 직접 import 없음
- [ ] 도메인 간 호출은 DTO/Interface로만 처리
- [ ] 외부 공개 API는 `/api/v1/**`, 내부 도메인 인터페이스는 Java Interface로 분리됨
- [ ] RAG, ChromaDB, 벡터 DB, Elasticsearch 코드/의존성 없음
- [ ] Kafka, Kubernetes, Helm v1 도입 없음
- [ ] AI 자유 챗봇, 다중 턴 대화, SSE, `/ai/sessions/**` 엔드포인트 없음
- [ ] 해설·시뮬레이터는 사용자 요청 경로에서 즉시 생성하지 않음
- [ ] F-15 사실 기반 Q&A는 단발·검증 흐름으로만 제공
- [ ] QT 본문은 00:00 KST 공개 / 04:00 KST 수집 배치 기준
- [ ] 오늘 QT 응답은 본문, 해설 진입점, 묵상 진입점, 시뮬레이터 상태값을 반환
- [ ] 성서 유니온·두란노 본문 텍스트 자체 저장 없음
- [ ] 성경 데이터는 Repo URL·라이선스·번역본명·출처 표기·재배포 가능 여부 확인 완료
- [ ] 개역개정, ESV, NIV 시드/응답/테스트 데이터 없음
- [ ] 찬양 가사·음원 저장 없음, AI 찬양 추천 없음
- [ ] 교회 인증 화면/버튼/API 없음
- [ ] 시뮬레이터는 `READY/MISSING/FAILED/DISABLED` 상태를 반환
- [ ] 관리자 API는 기능별 관리자 권한, 배치/AI 내부 작업은 `SYSTEM_BATCH` 권한 기준
- [ ] 인프로세스 이벤트 실패 시 `eventId` + error log 기록 및 재처리 가능
- [ ] `application.yml` 또는 코드에 평문 Secret 없음

## 테스트 체크리스트
- [ ] 단위 테스트(Unit Test) 작성 완료 및 `./gradlew -p qtai-server test` 로컬 통과
- [ ] 통합 테스트(Integration Test) 작성 완료
      또는 미작성 사유:
- [ ] docs / chore 타입은 해당 없음

## 테스트 방법
[어떻게 테스트했는지 설명: Unit / Integration / 수동 / 미실행 사유]
```

## Command Suggestions

Normal PR commands:

```powershell
git checkout dev
git pull origin dev
git checkout -b feature/<scope>-<short-description>
git push -u origin <current-branch>
gh pr create --base dev --head <current-branch> --title "<title>" --body "<body>"
```

Do not run these automatically unless explicitly asked. If already on a feature branch, do not suggest creating a new branch unless the current branch is `dev`, `master`, or incorrectly named.

Emergency hotfix commands are exceptional and require explicit user confirmation:

```powershell
git checkout master
git checkout -b hotfix/<short-description>
git push -u origin hotfix/<short-description>
gh pr create --base master --head hotfix/<short-description>
```

After a master hotfix is merged, remind the user that the fix must also be cherry-picked to `dev`.

## REQUEST_CHANGES Mode

When the user asks to handle review feedback:

1. Inspect the comments or ask the user to paste them.
2. Group comments by file and severity.
3. Separate true blockers from suggestions.
4. Propose a fix plan before editing unless the requested fix is trivial.
5. After changes, draft a Korean reply summary for each review thread.

Do not dismiss a reviewer comment without a technical reason. Do not rewrite unrelated files to satisfy broad feedback.

## Output Style

For PR preparation, respond in this order:

1. `사전 점검`: branch, base, size, risk findings.
2. `누락 정보`: only unresolved inputs needed before PR.
3. `PR 제목`: one recommended Korean Conventional Commit style title.
4. `PR 본문`: filled template.
5. `제안 명령`: push/create commands, clearly not executed unless already requested.

If a critical risk is found, put it before the PR draft and do not present the PR as ready.
