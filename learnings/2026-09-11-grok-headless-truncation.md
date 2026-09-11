# Grok headless 리뷰 호출이 조용히 중간에 끊김

- Date: 2026-09-11
- From: mid `plans/chatbot/progress-log.md` ("Switch Agent?" 최종 검증 라운드)
- Status: candidate

## Fact / decision

같은 리뷰 프롬프트로 `grok -p ... --permission-mode plan`을 3회 연속 실행했는데, 매번 파일
몇 개를 읽다가 문장 중간에서 끊기고 exit code 0으로 종료됨(stderr 없음). `--prompt-file` +
`--permission-mode plan`, `--prompt-file` + `--max-turns 40` 조합으로도 동일하게 재현됨.
직전 라운드(같은 세션, 같은 방식)에서는 정상적으로 끝까지 리뷰를 마쳤었음.

## Repro / when it matters

- 이전 라운드들의 지적 다수를 요약해 프롬프트에 포함한, 상대적으로 길고 누적된 컨텍스트를
  주는 후속 리뷰 라운드에서 발생.
- `--always-approve`로 우회를 시도했으나 Claude Code 쪽 auto mode classifier가 차단해서
  그 경로로는 검증하지 못함.

## Do not promote if

1회만 관측됨 — 재현 조건(프롬프트 길이? 세션 누적 상태? 일시적 서비스 이슈?)이 미확정.
같은 패턴이 다시 나오면 `rules/`로 승격 검토(예: "긴 리뷰 프롬프트는 라운드를 쪼개서 보낸다"
또는 "grok 헤드리스 호출은 항상 결과 줄 수를 확인해서 조기 종료를 감지한다").
