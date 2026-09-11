# 파일에 남기지 말 것

팀 harness · 앱 progress-log · 핸드오프 · 커밋 메시지 공통.

쓰지 않는다:

- AI/사람 대화 원문, 프롬프트 전문
- 구독 잔여 %, 사용량 한도 원문, 청구 계정·영수증·내역성 과금 정보
- 개인 메신저·내부 공유 채널 내용
- 개인정보·식별 정보: 실명, 직급/호칭, 이메일, 전화, 사번, 계정 ID
- API 키, 토큰, 인증서, 비밀번호 (MCP 예시는 **env 키 이름만**)

담당은 `담당-A` / 업무 범위 / `Grok`·`Claude`·`Codex` 라벨만.
파일에는 **결정 · 사실 · 파일 경로**만 남긴다.

예외: context/session 운영을 위해 필요한 provider 상태는 원문 대신 정규화해서 남길 수 있다.

- source: `ACTUAL` | `ESTIMATED` | `UNAVAILABLE`
- billingMode: `SUBSCRIPTION` | `API` | `UNKNOWN`
- costUsd: 실제 계산 가능할 때만 숫자, 아니면 `null`

subscription 기반 provider를 `$0`으로 기록하지 않는다.

앱 저장소에 더 긴 규정이 있으면 그쪽이 우선한다 (예: mid `AGENTS.md`).
