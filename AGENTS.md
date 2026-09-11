# harness-history — 이 저장소에서 일하는 AI에게

이 저장소는 앱 코드가 아니라 **팀 공통 스킬·규칙·연동 예시**만 둔다.

1. `README.md` 의 에이전트 프롬프트(공통 + 본인 도구)를 따른다.
2. `rules/` 전부와 필요한 `skills/*/SKILL.md` 를 직접 읽는다 (`rules/` 는 자동 주입되지 않음).
3. 앱 작업과 섞지 않는다. 앱 규칙은 앱 `AGENTS.md` 가 우선.
4. 시크릿·실명·잔여%·프롬프트 원문을 쓰지 않는다.
5. 커밋·푸시는 사용자 지시 후에만. 앱 레포 커밋과 섞지 않는다.
6. 긴 세션·고비용 usage·context exhaustion 징후가 있으면 `skills/context-session-budget/SKILL.md` 를 먼저 적용한다.

연결: `docs/connect-grok.md`, `docs/connect-agents.md`.
