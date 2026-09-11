---
name: session-start-check
description: >-
  Run before coding in a shared multi-agent checkout. Use when starting a slice,
  resuming work, opening a new AI session, or when the user says "세션 시작",
  "시작 전에", "핸드오프 확인", or "/session-start-check".
user-invocable: true
---

# Session start check

공유 체크아웃에서 코딩·검수에 들어가기 **전에** 아래만 확인한다. mid 앱 전용 세부 규칙은
해당 저장소 `AGENTS.md` / `plans/agent-collab-prompt.txt`가 우선한다.
새 PC·망·자리·세션이 바뀐 뒤에는 `docs/location-and-space.md` 도 참고한다.

## Steps

1. **Git 상태**
   - `git status`로 merge / cherry-pick / rebase 진행 중인지 본다.
   - 진행 중이면 이어서 커밋하거나 함부로 되돌리지 말고, 원인을 먼저 파악한다.

2. **Harness pull + 규칙**
   - harness 경로를 모르면 사용자에게 한 번 묻는다. 앱이 `C:\mid\mid` 이면 후보로 형제 `C:\mid\harness-history` 만 확인한다.
   - clone 이 없으면 코딩에 들어가지 말고 경로/clone 을 요청한다.
   - clone 이 있으면 그 디렉터리에서 `git pull`. 실패하면 이유를 말하고 **코딩을 시작하지 않는다**.
   - `rules/` 전부와 (역할에 맞게) 필요 스킬을 **직접** 읽는다. `rules/` 는 도구가 자동으로 안 넣는다.
   - 앱 레포도 작업 전에 pull 여부를 확인한다 (다른 세션 커밋을 놓치지 않기).

3. **누적 맥락**
   - 활성 주제의 `plans/<주제>/progress-log.md`(또는 팀이 지정한 동등 로그)를 읽는다.
   - 이미 내린 결정·오탐·보류 하드닝을 다시 뒤집지 않는다.

4. **범위**
   - 핸드오프/슬라이스에 `건드릴 파일:`이 있으면 **그 목록 밖은 수정하지 않는다**.
   - 없으면 사용자에게 범위를 확인한 뒤 진행한다.

5. **민감 정보**
   - 파일·커밋·progress-log에 쓰지 말 것: 대화/프롬프트 원문, 구독 잔여·과금, 실명·직급·연락처·사번·계정 ID, 내부 메신저 내용.
   - 담당은 `담당-A` / 역할 라벨 또는 `Grok`/`Claude`/`Codex`만. 파일에는 결정·사실·경로만.

## Done when

위 1–5를 확인했고, 막히면 코딩을 시작하지 않은 채 사용자에게 막힌 이유를 한 줄로 알린 상태.
