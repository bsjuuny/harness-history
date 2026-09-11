---
name: context-session-budget
description: >-
  Check context usage, session checkpoint/rollover need, and provider usage
  signals before continuing a long Claude, Codex, Grok, or multi-agent coding
  session. Use when context is high, usage is expensive, a handoff is needed,
  or the user mentions context, token, cache read, checkpoint, rollover, or
  session exhaustion.
user-invocable: true
---

# Context and session budget

긴 세션을 계속 이어갈지, checkpoint/rollover/handoff로 끊을지 판단한다.
새 Harness 구조를 만들지 말고, 앱 저장소와 현재 도구가 이미 제공하는 session, usage,
router, provider abstraction, log 구조가 있으면 먼저 재사용한다.

## Thresholds

| Level | Threshold | Action |
|------|-----------|--------|
| warning | 70% | 사용자에게 세션이 커지고 있음을 알리고 긴 출력·반복 스캔을 줄인다. |
| checkpoint | 80% | checkpoint 요약을 남긴다. |
| rollover | 85% | 새 세션 전환을 준비한다. 이전 대화 전체를 넘기지 않는다. |
| hardLimit | 92% | 새 작업을 시작하지 말고 rollover 한다. |

값을 실제로 알 수 없으면 추정치를 사실처럼 쓰지 않는다.

## Usage Data Labels

usage, context, cost 값은 반드시 출처 등급을 붙인다.

- `ACTUAL`: 도구/API/status 출력에서 직접 확인한 값
- `ESTIMATED`: 파일 크기, 메시지 수, known context window 등으로 계산한 추정
- `UNAVAILABLE`: 현재 도구로 얻을 수 없음

과금은 다음처럼 기록한다.

- `billingMode`: `SUBSCRIPTION` | `API` | `UNKNOWN`
- `costUsd`: 실제 계산 가능할 때만 숫자, 아니면 `null`
- subscription 기반 provider는 비용을 `$0`으로 쓰지 않는다.

## Provider Checks

Claude:
- cache read가 cache write보다 비정상적으로 크거나, 같은 거대 context가 1,000회 이상 반복 호출되는지 본다.
- repeated cache rebuild, oversized session, repeated long tool output, 동일 repository scan 반복을 확인한다.
- 긴 tool output과 해결된 오류 로그는 다음 handoff에서 제외한다.

Codex:
- context window 사용률, session age, reasoning-heavy task 누적, 반복 repository scan을 확인한다.
- 80% 이상이면 checkpoint, 85% 이상이면 rollover 준비, 92% 이상이면 hard rollover로 본다.

Grok:
- 현재 integration에서 실제 제공하는 usage/status만 사용한다.
- 제공하지 않는 context/cost 값은 `UNAVAILABLE`로 둔다.

## Checkpoint Format

Session rollover 때 이전 conversation 전체를 전달하지 않는다. 아래만 남긴다.

```text
current task:
completed work:
changed files:
architecture decisions:
constraints:
test results:
known issues:
remaining tasks:
next action:
usage/status:
- provider:
- context: ACTUAL|ESTIMATED|UNAVAILABLE
- billingMode:
- costUsd:
```

제외: 긴 tool output, 과거 로그 원문, 해결된 오류 반복 출력, 내부 메신저 원문,
프롬프트 원문, 실명·직급·계정 ID, 구독 잔여율.

## Routing Guidance

- provider/router/session abstraction이 이미 있으면 새 모듈을 만들지 말고 기존 abstraction을 확장한다.
- context health가 `hardLimit`이면 해당 provider에 새 작업을 배정하지 않는다.
- `rollover` 이상이면 새 세션 전환 또는 압축 handoff를 먼저 만든다.
- rate limit, provider outage, context exhaustion은 fallback 사유로 기록하되, 실제 사용 불가능한 값을 만들어내지 않는다.

## Done when

현재 provider별 context/usage 상태가 `ACTUAL|ESTIMATED|UNAVAILABLE`로 표시됐고,
warning/checkpoint/rollover/hardLimit 중 필요한 조치가 결정됐으며,
handoff가 필요하면 위 checkpoint format으로 짧게 정리된 상태.
