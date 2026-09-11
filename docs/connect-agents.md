# Claude Code · Codex · 기타 연결

Grok 전용 설정은 `connect-grok.md`. 여기는 **미러/플러그인 없이** 쓰는 기본 방법입니다.

## 전제

| 이름 | 예시 |
|------|------|
| 앱 | `C:\mid\mid` |
| harness (이 레포) | `C:\mid\harness-history` |

앱 `AGENTS.md`에 harness 경로가 있어야, 앱만 열어도 이 저장소를 찾을 수 있습니다.

## 매 세션

1. harness에서 `git pull`
2. 직접 읽기 (절대경로):
   - `.../harness-history/README.md`
   - `.../harness-history/rules/` (전부)
   - `.../harness-history/skills/session-start-check/SKILL.md`
3. 앱 `AGENTS.md` + 활성 `progress-log`
4. (선택) README의 해당 에이전트 프롬프트를 세션에 붙이기

`.claude/skills` 등으로 복사·심볼릭 링크는 **선택**입니다. 쓰면 읽기만 하고, **앱 레포에 harness 파일을 커밋하지 마세요.**

## 커밋

앱과 harness는 **별도 git 저장소**입니다.

- `git status` / `commit` / `push` 를 레포 폴더마다 따로
- 한 세션에서 둘 다 고쳐도 커밋을 섞지 않음
- 사용자 지시 전에는 커밋·푸시하지 않음

## Codex (검수)

- 기본 역할이 검수면 `skills/slice-review-checklist/SKILL.md` 를 따름
- 판정은 `통과|부분통과|반려` + 수정 목록만 (재구현 금지)
- 근거 인용: `rules/<file>` 또는 앱 `AGENTS.md` / 핸드오프 항목명
- 앱 규칙과 겹치면 앱 우선. 충돌 시 harness 를 고치지 않음
- 건드릴 파일에 harness 경로가 없고 승격 지시가 없으면 harness 편집 금지 (승격 후보 한 줄만)
- 상세 프롬프트: README «Codex» 절
