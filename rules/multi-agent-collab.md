# 멀티 에이전트 · 사람 협업 (요약)

여러 사람/AI가 같은 체크아웃에서 일할 때.

- 시작 전: `git status` (merge/cherry-pick/rebase), 주제 `progress-log` 읽기.
- 핸드오프에 `건드릴 파일:`이 있으면 목록 밖은 수정하지 않는다.
- 한 슬라이스(블록)는 검수 결과(통과|부분통과|반려) 한 줄로 종결. 이후 그 블록 편집 금지.
- progress-log / 핸드오프 접두: `[작성자] YYYY-MM-DD:` — 작성자는 AI 이름 또는 역할 라벨. 실명·직급 금지.
- 커밋 전 `git status` / `git diff`로 다른 세션 작업을 덮지 않았는지 확인.
- harness(skill/rule/MCP 예시) 변경은 리뷰 후 merge. 멤버는 다음 세션 전 `git pull`.

프로젝트별 상세(역할 배분, break-list, 잔여% 운용)는 **앱 레포** 협업 문서가 우선한다.
(예: mid `plans/agent-collab-prompt.txt`)
