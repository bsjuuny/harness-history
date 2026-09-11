# Grok headless(`grok -p`/`--prompt-file`) 리뷰 호출이 조용히 중간에 끊길 수 있다

`grok -p`(또는 `--prompt-file`)로 읽기 전용 리뷰를 시킬 때, 파일 몇 개를 읽다가 문장 중간에서
끊기고 **exit code 0, stderr 없이 조용히 종료**되는 경우가 있다. 2026-09-11 하루에만 서로 다른
두 리뷰 주제(프론트 챗봇 기능 / 백엔드 Java 인증 로직)에서 10회 이상 관측됨.

## 원인 1(확인·해결됨): 프로젝트가 "trusted"가 아님

`grok inspect`로 확인하면 `Project trusted: no`로 뜨는 경우가 있다 — `~/.grok/trusted_folders.toml`에
그 정확한 경로 항목이 없어서다(부모 폴더가 trusted여도 자식 폴더는 별개 항목으로 취급되는 것으로
보임 — 예: `C:\mid`는 있는데 `C:\mid\mid`는 없어서 후자에서 실행하면 untrusted였음).

**해결:** `~/.grok/trusted_folders.toml`에 같은 형식으로 항목을 직접 추가하면 된다(원본을
백업해두고 추가할 것):

```toml
[folders.'<정확한 경로, 예: C:\mid\mid'>]
trusted = true
decided_at = <아무 epoch 정수>
```

추가 후 `grok inspect`(그 디렉터리에서 실행)로 `Project trusted: yes`인지 먼저 확인한다.

## 원인 2(미해결): `run_terminal_command` 도구 호출이 헤드리스에서 항상 PermissionCancelled

원인 1을 고친 뒤에도(트러스트 확인됨) 여전히 끊기는 경우가 있었다. `--debug-file`로 받은 디버그
로그를 보면 `session/prompt` 응답이 `"stopReason":"cancelled"`, `"cancellationCategory":
"PermissionCancelled"`로 오고, 바로 직전 로그에 `Model requesting tool: name='run_terminal_command'`가
찍혀 있다 — 즉 Grok이 리뷰 중 셸 명령을 쓰려고 하면, 그 특정 도구 호출에 대한 승인을 헤드리스가
줄 수 없어서 세션 전체가 취소된다.

`--permission-mode plan` / `dontAsk` / `auto` 전부 이 특정 도구(run_terminal_command)에 대해서는
동일하게 취소됨을 확인함 — 즉 이 옵션들로는 못 피한다. `--always-approve`는 Claude Code 쪽
안전장치가 막아서 시도하지 않는다.

**How to apply:**
- Grok 리뷰 결과가 한두 문장짜리 진행 메시지만 있고 실제 지적 목록이 없으면, "지적 없음"으로
  해석하지 말고 **끊긴 것으로 간주**한다.
- 먼저 `grok inspect`(대상 디렉터리에서)로 "Project trusted"를 확인 — no면 원인 1의 해결법 적용
  후 재시도.
- 그래도 안 되면 원인 2(터미널 명령 권한)일 가능성이 높다 — 프롬프트에 "셸/터미널 명령 쓰지 말고
  파일만 읽어서 리뷰해줘"처럼 명시적으로 제한하는 문구를 넣어보는 것도 시도해볼 만하다(아직
  검증 안 함 — 다음에 이 문제가 나오면 시도해보고 결과를 여기 추가할 것).
- 그래도 안 되면 더 반복하지 말고, Codex/Claude 결과만으로 진행하되 사용자에게 "Grok 결과 못
  받음"을 명시적으로 알린다 — 조용히 생략하지 않는다.

Source: mid `plans/chatbot/progress-log.md` (LEARNING, 2026-09-11 — "Switch Agent?" 최종 검증
라운드 + PDA agent Google ID token 인증 리뷰 라운드, 총 10회 이상 관측. 원인 1은
`~/.grok/trusted_folders.toml` 직접 확인으로 특정·해결. 원인 2는 `--debug-file` 로그로 특정했으나
회피 방법은 아직 못 찾음)
