# 로컬 머신에 Claude Code / Codex / Grok CLI가 모두 설치·인증되어 있음

새 세션에서 "Codex/Grok CLI가 설치돼 있나요?" 또는 "인증돼 있나요?"를 다시 묻지 않는다.
이 머신에서는 세 도구 모두 헤드리스 호출까지 확인됨(2026-09-13).

## 확인된 사실

| 도구 | 상태 | 비고 |
|------|------|------|
| Claude Code | 설치·인증됨 | 이 harness를 읽고 쓰는 주 도구 — 항상 있다고 가정 |
| Codex CLI | 설치·인증됨 | `codex-cli 0.154.0`, `~/.codex/auth.json` 존재 |
| Grok CLI | 설치·인증됨 | `grok 1.0.30`, `grok doctor` 0 issues |

## How to apply

- 기본값: 셋 다 설치·인증 완료 상태로 간주하고 바로 헤드리스 호출을 시도한다.
- 그래도 호출이 실패하면 그때만 `codex doctor` / `grok doctor`로 재확인한다.
- 다른 사람·다른 머신도 동일하다고 가정하지 않는다 — 이 표는 "적어도 이 머신에서는
  확인됨"만 보장한다.

## 확인된 헤드리스 호출법

### Codex

```
codex exec --skip-git-repo-check -s read-only -C <dir> -o <output-file> "<prompt>"
```

- `--skip-git-repo-check` 없으면 "Not inside a trusted directory" 에러로 즉시 중단됨
  — 대상 디렉터리가 실제 git repo여도 발생할 수 있음(Codex 자체 trust 개념이 git repo
  여부와 별개인 것으로 보임).
- `-s read-only`로 조사/리뷰 목적 제한 가능. `-o <file>`로 최종 답변만 깔끔히 파일로
  받을 수 있음(중간 reasoning/tool trace는 stdout에 섞여 나옴 — 최종 답변만 필요하면
  stdout은 로그 파일로 리다이렉트하고 `-o` 결과만 읽는다).
- **프롬프트를 CLI 인자로 직접 넘기면 700줄 안팎(diff를 통째로 넣는 리뷰 요청 등)에서
  Windows에서 `Argument list too long`로 죽는다**(관측: 751줄짜리 프롬프트에서 재현,
  435줄짜리는 괜찮았음 — 정확한 임계값은 그 사이 어딘가). 큰 프롬프트(특히 diff를
  통째로 붙이는 리뷰 요청)는 인자 대신 stdin으로 넘긴다:
  `codex exec --skip-git-repo-check -s read-only -C <dir> -o <output-file> < prompt.txt`
  (프롬프트 인자를 생략하면 stdin에서 읽음).

### Grok

```
grok -p "<prompt>" --cwd <dir> --always-approve
```

- 셸/파일 도구를 안 쓰는 단순 프롬프트는 트레이스 없이 최종 답변만 stdout에 출력됨.
- `rules/grok-headless-review-caveat.md`의 "긴 리뷰나 셸 도구 호출 시 조용히 끊김" 이슈는
  별개로 여전히 유효함 — 여기서 확인한 건 "CLI 자체가 정상 설치·인증되어 응답한다"뿐이고,
  저 caveat(특히 원인 2: `run_terminal_command` 헤드리스 승인 불가)을 대체하지 않는다.

## Do not promote if

해당 없음 — 이건 실험 결과가 아니라 이 머신의 현재 상태를 적은 참고 사실이라 rules에 바로
둠(learnings 승격 절차 불필요). 도구를 재설치/재인증하거나 다른 머신으로 옮기면 이 표를
갱신할 것.
