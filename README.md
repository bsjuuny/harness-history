# harness-history

여러 사람·여러 AI 코딩 도구가 **같은 절차·규칙·연동 목록**을 쓰도록 모아 두는 저장소입니다.
앱 소스(예: mid)와는 따로 둡니다.

한 사람이 skill·규칙을 고쳐서 리뷰·병합하면, 다른 사람은 이 저장소를 `git pull` 해서 같은 내용을 받습니다.
개인 노트북에만 있는 설정은 팀에 안 남습니다. 여기에 올린 것만 팀 기준으로 유지됩니다.

## 이 레포에 넣는 것

| 경로 | 역할 |
|------|------|
| `skills/<name>/SKILL.md` | 팀 공통 절차 (반복해서 하는 일) |
| `rules/*.md` | 짧은 팀 규칙 |
| `docs/mcp.team.example.toml` | 외부 도구 연동(MCP) 이름·필요 환경변수 **키 이름만** (값 없음) |
| `learnings/` | 아직 skill/규칙으로 안 굳힌 팀 메모 |

## 넣지 않는 것

- 앱 비즈니스 코드
- API 키·토큰·인증서·비밀번호
- 실명·직급·이메일·사번·계정 ID
- 대화/프롬프트 원문, 구독 잔여%, 내부 메신저 내용

## 멤버 연결

1. 이 저장소를 clone (예: 앱이 `C:\mid\mid` 이면 형제로 `C:\mid\harness-history`)
2. **앱 `AGENTS.md`에 harness 경로·pull 한 줄**이 있어야 함 (앱만 열어도 핸드북을 찾음)
3. 작업 시작 전 이 레포에서 `git pull`
4. 도구별:
   - **Grok:** `docs/connect-grok.md` — `~/.grok/config.toml`에 `[skills] paths`
   - **Claude / Codex / 기타:** `docs/connect-agents.md` — 미러 없이 **절대경로로 직접 읽기**가 기본

중요: `rules/*.md` 는 대부분 도구가 **자동으로 안 읽습니다.** 세션마다 직접 열거나, 아래 프롬프트를 붙이세요.
팀 스킬/규칙을 쓸 때는 개인 `~/.grok/skills` 나 앱 `.grok/skills` 가 아니라 **이 레포**에 씁니다.

## 변경할 때

1. 브랜치에서 skill / rule / MCP 예시 수정
2. PR 또는 팀 검수 — **검토 없이 main에 바로 넣지 않음**
3. 병합 후 멤버 `git pull`
4. 잘못된 기본값이 전 팀 AI에 퍼지지 않는지 검수자가 확인

한 사람의 실수가 전 팀 기본 동작이 되지 않게, 리뷰 후에만 공유합니다.

## 경험 쌓기

앱 작업 로그(progress-log 등)에 같은 실수·결정이 반복되면, 그 내용만 골라
이 레포의 `skills/` 또는 `rules/`로 올립니다. 절차: `skills/promote-learning/SKILL.md`.

## 위치·공간 변화

새 PC, 재택/사내망, 자리 이동, 세션·담당 교대에도 같은 기준으로 이어가려면
`docs/location-and-space.md` 를 읽는다 (온보딩, 망별 허용 표, catchup 템플릿).

---

## AI 에이전트용 프롬프트

세션에 붙여 넣기 전에 경로만 본인 환경에 맞게 고칩니다.

| 자리표시 | 의미 | 예시 |
|----------|------|------|
| `<HARNESS>` | 이 팀 harness clone 경로 | `C:\mid\harness-history` |
| `<APP>` | 앱 저장소 경로 (앱 작업일 때) | `C:\mid\mid` |

경로를 모르면 사용자에게 한 번만 묻고, 추측으로 다른 디스크·홈을 훑지 마세요.

### 공통 (읽기 + 쓰기)

```text
너는 팀 harness(<HARNESS>)의 규칙·스킬을 읽고, 필요할 때만 거기에 팀 공통 내용을 쓴다.
앱 작업이면 <APP> 도 함께 본다.

경로:
- <HARNESS>·<APP> 를 모르면 사용자에게 한 번 묻는다
- 예: 앱이 C:\mid\mid 이면 harness 후보는 형제 C:\mid\harness-history 만 확인한다. 없으면 묻는다 (다른 디스크·홈을 훑지 않는다)

시작:
- <HARNESS>에서 git pull 한다 (실패하면 이유를 말하고 멈춘다)
- <HARNESS>/README.md, <HARNESS>/rules/ 전부, <HARNESS>/skills/session-start-check/SKILL.md 를 읽는다 (rules 는 자동 로드되지 않음 — 반드시 직접 연다)
- 긴 세션, context 70% 이상, cache read 폭증, provider fallback/rollover 이슈가 있으면 <HARNESS>/skills/context-session-budget/SKILL.md 도 읽는다
- LEARNING 승격 요청이면 <HARNESS>/skills/promote-learning/SKILL.md 도 읽는다
- 검수 요청이면 <HARNESS>/skills/slice-review-checklist/SKILL.md 도 읽는다
- 앱에서 일하면 <APP>/AGENTS.md 와 활성 progress-log 만 더 읽는다
- CLAUDE.md 가 없어도 «규칙 없음»이 아니다. 앱 계약 정본은 <APP>/AGENTS.md 다
- 핸드오프에 «건드릴 파일»이 있으면 그 밖은 수정하지 않는다
- harness에 없는 팀 규칙을 지어내지 않는다. 앱 규칙과 겹치면 <APP> 이 우선이다

쓸 때:
- 앱 비즈니스 코드는 <HARNESS>에 넣지 않는다 (<APP>에서만)
- 새 팀 skill/rule 은 ~/.grok/skills, <APP>/.grok/skills, 개인 홈이 아니라 <HARNESS>/skills|rules 에만 쓴다
- 여러 스텝 → <HARNESS>/skills/<name>/SKILL.md
- 짧은 규칙 → <HARNESS>/rules/
- 미확정 → <HARNESS>/learnings/
- 이미 있으면 새 파일 말고 기존에 합친다
- 키·토큰·실명·잔여%·프롬프트 원문·내부 메신저는 쓰지 않는다
- usage/cost/context는 원문·잔여율을 붙이지 말고 ACTUAL|ESTIMATED|UNAVAILABLE, billingMode, costUsd(null 가능)로만 정규화한다
- <APP> 과 <HARNESS> 는 별도 git 이다. status/diff/commit/push 는 레포별로 분리하고, 제안 시 레포 경로를 명시한다
- 작성 후 diff 요약만 보여 준다. 커밋·푸시는 사용자가 시킨 뒤에만
- 작성자 표기는 [Grok|Claude|Codex|담당-라벨] YYYY-MM-DD: (실명 금지)
```

### Grok

```text
위 «공통»을 따른다. <HARNESS> · <APP> 경로를 사용한다.
- ~/.grok/config.toml 의 [skills] paths 에 <HARNESS>/skills 가 있으면 쓰고, 없으면 그 경로를 직접 읽는다
- 팀 규칙(<HARNESS>/rules)은 paths 로 안 들어온다. 항상 직접 읽는다
- /create-skill 기본 저장 위치를 쓰지 말고, 팀 공유분은 <HARNESS>/skills 에만 쓴다
- session-start-check / promote-learning / slice-review-checklist 를 역할에 맞게 사용한다
- skill 을 쓸 때 description 에 언제 쓰는지 적는다
```

### Claude Code

```text
위 «공통»을 따른다. <HARNESS> · <APP> 경로를 사용한다.
- cwd 가 <APP> 이어도 팀 핸드북은 <HARNESS> 다. 세션 초에 README·rules·session-start-check 를 절대경로로 직접 읽는다
- cache read 폭증, repeated cache rebuild, oversized session, repeated long tool output 이 보이면 context-session-budget 기준으로 checkpoint/rollover를 판단한다
- .claude 미러는 없어도 된다. 있어도 앱 레포에 harness 파일을 커밋하지 않는다
- <APP>/AGENTS.md 를 반드시 읽는다. CLAUDE.md 부재를 규칙 없음으로 해석하지 않는다
- <APP> 과 <HARNESS> 커밋을 섞지 않는다. 커밋 제안 시 어느 레포인지 경로로 명시한다
```

### Codex

```text
위 «공통»을 따른다. <HARNESS> · <APP> 경로를 사용한다.
- 기본은 검수. 핸드오프에 구현 담당=Codex 일 때만 구현 모드.
- context window 사용률·session age·반복 repository scan·reasoning-heavy 누적을 확인하고 80% checkpoint, 85% rollover, 92% hard rollover를 적용한다
- 검수: <HARNESS>/skills/slice-review-checklist 를 따른다. diff만. 재구현 금지.
- 판정 한 줄: 통과|부분통과|반려 + 수정 목록. 근거에 rules/<file> 또는 <APP>/AGENTS.md / 핸드오프 항목명을 붙인다.
- <HARNESS> 와 <APP> 규칙이 충돌하면 <APP> 우선. 사유에 «앱 우선, harness는 참고»를 쓰고 harness 를 고치지 않는다.
- harness 편집 금지: 건드릴 파일에 <HARNESS> 경로 없음 / 앱 코드 검수·구현만 요청 / 규칙 부족만 보일 때(그때는 승격 후보 한 줄만).
- harness 를 만질 때: 초안·정리·LEARNING 승격 초안만. <APP> 큰 구현과 커밋을 섞지 않는다.
```

### Cursor / 기타

```text
위 «공통»을 따른다. <HARNESS> · <APP> 경로를 사용한다.
- 팀 핸드북은 <HARNESS>, 앱 코드는 <APP>. docs/connect-agents.md 를 본다.
- provider가 실제로 제공하지 않는 usage/context/cost 값은 만들지 말고 UNAVAILABLE로 둔다.
- 팀 공통만 harness에 쓴다. 커밋은 레포별.
```

### 역할 한 줄 (필요할 때만 붙이기)

| 역할 | 한 줄 |
|------|--------|
| 조사 | 넓게 바꾸지 말고 사실·선택지만. |
| 구현 | 건드릴 파일만. <HARNESS> 와 <APP> 커밋은 분리. |
| 검수 | diff·완료조건만. 재구현 금지. 판정에 rules/AGENTS 경로 인용. harness는 승격 후보만(지시 없으면 편집 금지). |
