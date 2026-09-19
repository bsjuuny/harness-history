# 상시 실행 프로세스의 진입 모듈을 검증용으로 import 하면 인스턴스가 하나 더 뜬다

- Date: 2026-09-19
- From: risk-alert-bot 검증 세션 (Claude Code)
- Status: candidate

## Fact / decision

pm2 로 상시 실행 중인 텔레그램 봇을 검증하면서, 테스트 스크립트에서 진입 모듈을
`await import('./src/bot.mjs')` 했다. 주석에 "실행하지 않음"이라고 적어 두었지만 ESM 은
import 만으로 최상위 코드가 전부 실행된다 — 폴링 루프와 `setInterval` 들이 그대로 돌기
시작해 **두 번째 봇 인스턴스가 약 1시간 동안 살아 있었다.**

증상은 텔레그램 API 의 `Conflict: terminated by other getUpdates request` 가 10초마다
반복되는 것이었고, 그 사이 알림이 중복 발송됐을 수 있다. pm2 목록에는 앱이 하나로만
보여서(직접 띄운 프로세스는 pm2 가 모른다) 한참 뒤에야 발견했다.

원인 프로세스는 다음으로 찾았다. **CommandLine 까지 봐야 한다** — pm2 가 포크한
프로세스는 cmdline 에 스크립트 경로가 안 나오지만, 직접 띄운 `node -e` 는 코드가 그대로
보인다.

```powershell
Get-CimInstance Win32_Process -Filter "Name='node.exe'" |
  ForEach-Object { "$($_.ProcessId) | $($_.CreationDate) | $($_.CommandLine)" }
```

## Repro / when it matters

- 진입 모듈(부작용 있는 최상위 코드: 폴링 루프, 타이머, 외부 연결, 서버 listen)을 가진
  프로젝트를 **운영 중에** 검증할 때.
- 단일 인스턴스를 전제하는 외부 서비스(텔레그램 long-poll, 파일 락, 단일 소비자 큐)를
  쓰면 즉시 충돌이 나지만, 그렇지 않은 경우엔 **조용히 중복 작업만 수행**해서 더 늦게
  발견된다.
- Node/ESM 뿐 아니라 Python `import` 도 같다(모듈 최상위 코드 실행).

대응:
- 검증은 순수 모듈만 import 한다. 진입 모듈의 로직이 필요하면 그 함수를 별도 모듈로
  분리하거나, 로직을 테스트 스크립트에 복제해 재현한다.
- 진입 모듈에 부작용을 두어야 한다면 `if (import.meta.url === pathToFileURL(process.argv[1]).href)`
  (Node) / `if __name__ == "__main__":` (Python) 로 감싸 두면 import 해도 안전하다.
- 운영 프로세스를 건드리는 검증 뒤에는 프로세스 수를 확인한다.

## Do not promote if

관측 1회. 다만 원인·재현 조건이 명확하고 도구/언어를 가리지 않는 함정이라,
같은 사고가 한 번 더 나오면 `rules/` 로 승격 검토.
