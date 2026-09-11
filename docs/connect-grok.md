# Grok Build에 harness 연결

## 1. Clone

```text
git clone https://github.com/bsjuuny/harness-history.git
```

예: `C:\mid\harness-history` 또는 `%USERPROFILE%\harness-history`

## 2. skills 경로 등록

`%USERPROFILE%\.grok\config.toml` (또는 `$GROK_HOME/config.toml`):

```toml
[skills]
paths = ["C:/mid/harness-history/skills"]
```

- Windows 경로는 `/` 또는 이스케이프된 `\\` 사용.
- clone 위치가 다르면 그 경로의 `skills` 폴더를 가리킨다.
- 여러 경로를 나열할 수 있다.

`[skills] paths`는 **skills만** 스캔한다. `rules/*.md` 는 자동으로 안 들어온다 —
세션마다 `<HARNESS>/rules/` 를 직접 읽거나 README 프롬프트를 붙인다.
앱 `AGENTS.md`에 harness 경로·pull 안내가 있어야 mid만 열어도 핸드북을 찾는다.

## 3. 매 세션

```text
cd <harness-history>
git pull
```

그다음 앱 레포에서 작업. skill이 안 보이면 Grok을 재시작하거나 `/skills`로 목록을 확인한다.

## 4. 앱 레포와의 관계

| 레포 | 역할 |
|------|------|
| `harness-history` | 팀 공통 skill / rule / MCP 예시 / learning |
| `mid` 등 앱 | 코드, `AGENTS.md`, 주제별 `progress-log` |

앱 전용 규칙은 앱 `AGENTS.md`에 두고, 여러 앱에 공통인 절차만 이 harness로 올린다.
