---
name: promote-learning
description: >-
  Promote a repeated LEARNING from a project progress-log into a team skill or
  rule in this harness repo. Use when the user says "LEARNING 승격", "팀 스킬로",
  "harness에 올려", or "/promote-learning".
user-invocable: true
---

# Promote learning to harness

앱 레포에만 있던 반복 경험을 **팀 harness**(`skills/` 또는 `rules/`)로 옮긴다.
시크릿·개인식별 정보는 승격하지 않는다.

## When to promote

다음 중 하나일 때:

- 같은 `LEARNING`이 progress-log에 **2회 이상** 등장
- 검수자가 “팀 기본으로 승격”이라고 명시
- 새 멤버/AI가 같은 踩坑을 다시 밟을 위험이 큼

승격하지 않음: 일회성 버그, 특정 환경 임시 우회, 민감·내부-only 맥락.

## Steps

1. **출처 확인**
   - progress-log(또는 동등 로그)에서 LEARNING 원문·날짜·작성자 라벨을 읽는다.
   - 민감 필드가 있으면 제거하고, 결정·사실·경로·재현 절차만 남긴다.

2. **저장 위치 선택**
   - **절차(여러 스텝)** → `skills/<name>/SKILL.md`
   - **짧은 상시 규칙** → `rules/<topic>.md`
   - 이미 같은 내용의 skill/rule이 있으면 **새 파일 만들지 말고** 기존에 병합한다.

3. **작성**
   - skill이면 YAML frontmatter(`name`, `description`에 트리거 문구) + 실행 스텝.
   - rule이면 한 주제만, mid `AGENTS.md`와 중복이면 “앱 AGENTS가 우선, 여기는 요약”을 명시.

4. **검증**
   - 시크릿·실명·잔여%·프롬프트 원문이 파일에 없는지 검색한다.
   - README의 거버넌스대로 **PR/검수 후 merge** (이 skill은 파일을 준비하고, push 강요하지 않음).

5. **출처 기록**
   - 새 skill/rule 하단에 `Source: <repo> plans/.../progress-log.md (LEARNING, YYYY-MM-DD)` 한 줄.
   - 앱 progress-log에 `[작성자] YYYY-MM-DD: harness로 승격 — skills/<name>` 한 절을 남기라고 안내한다.

## Done when

draft skill 또는 rule 파일이 harness 워킹트리에 있고, 민감값 없이, 사용자가 내용을 보고 커밋/PR할지 결정할 수 있는 상태.
