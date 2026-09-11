# Codex/Grok "읽기 전용" 지시는 강제되지 않는다

"읽기 전용"·"수정하지 마"라고 명시해도 Codex/Grok CLI 호출이 파일을 쓸 수 있다
(적어도 2회 독립 관측: mid `plans/chatbot/progress-log.md` 2026-09-10, 2026-09-11).

- 검수/조사 목적으로 Codex(`codex exec -s read-only`)나 Grok(`grok -p --permission-mode plan`
  등)을 부를 때도, 호출 전후로 반드시 `git status`(필요하면 `git diff`)를 비교한다.
- 예상 밖 변경이 보이면 곧바로 되돌리지 말고 내용부터 확인한다 — 관련 없어 보여도 다른
  세션/사람이 의도적으로 진행 중인 작업일 수 있다(실제로 그랬던 사례 있음).
- 이 caveat은 도구가 내놓는 리뷰 결과의 유용성과는 별개다: 결과는 참고하되, "읽기 전용"
  지시 자체를 보장으로 받아들이지 않는다.

Source: mid `plans/chatbot/progress-log.md` (LEARNING, 2026-09-10 Grok 2차 검수 도구 사고 /
2026-09-11 "Switch Agent?" 최종 검증 라운드)
