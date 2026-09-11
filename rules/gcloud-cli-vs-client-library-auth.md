# gcloud CLI는 `GOOGLE_APPLICATION_CREDENTIALS`를 안 본다

`gcloud auth print-identity-token`, `gcloud run services describe` 같은 `gcloud <서비스> <동작>`
명령은 `GOOGLE_APPLICATION_CREDENTIALS` 환경변수를 **무시**하고, gcloud 자체의 활성 계정
(`gcloud auth list`의 `ACTIVE`, `core/account` 설정)만 쓴다. 그 환경변수는 **클라이언트
라이브러리**(Java `GoogleCredentials.getApplicationDefault()`, Python `google-auth` 등)가
Application Default Credentials를 찾을 때만 유효하다.

**증상:** 다른 서비스 계정 키 파일 경로로 `GOOGLE_APPLICATION_CREDENTIALS`를 바꿔 설정한 뒤
`gcloud auth print-identity-token`을 돌려도, 발급된 토큰을 디코드해보면 여전히 gcloud의 기존
활성 계정 신원으로 나온다 — 환경변수가 조용히 무시된 것.

## 다른 서비스 계정으로 "한 번만" 테스트하고 싶을 때

전역 상태(활성 계정)를 안 바꾸는 순서:

1. **impersonation이 되면 그게 제일 깔끔하다**: `--impersonate-service-account=<대상 SA 이메일>`을
   해당 gcloud 명령에 붙인다(`gcloud auth print-identity-token --impersonate-service-account=... --audiences=...`
   등). 단, 현재 활성 계정에 대상 SA에 대한 `roles/iam.serviceAccountTokenCreator`가 있어야 한다 —
   없으면 `PERMISSION_DENIED: iam.serviceAccounts.getAccessToken`으로 즉시 실패한다(원인이 명확해서
   좋음).
2. **키 파일을 직접 갖고 있고 impersonation 권한이 없을 때**: gcloud CLI로는 안 되고
   (`gcloud auth activate-service-account --key-file=...`는 활성 계정을 영구히 바꿔버림 —
   되돌리려면 다시 activate 해야 함, 다른 작업에 영향 줄 수 있어 신중해야 함), 대신 **클라이언트
   라이브러리를 직접 쓰는 작은 스크립트**로 그 키 파일을 그 자리에서만 로드해서 쓴다:
   - Python: `google.oauth2.service_account.Credentials.from_service_account_file(key_path, scopes=[...])`
     (일반 API 접근) 또는 `IDTokenCredentials.from_service_account_file(key_path, target_audience=...)`
     (ID token, Cloud Run 호출용).
   - `credentials.refresh(google.auth.transport.requests.Request())` 후 `credentials.token`으로
     값을 꺼내 `requests`/`urllib`로 직접 호출한다.
   - 이 방법은 gcloud의 `core/account`를 전혀 안 건드리므로 세션이 끝나면 아무 흔적도 안 남는다.
3. 두 방법 다 안 되면(키 파일도 없고 impersonation 권한도 없음) 그건 실제 권한 문제이니 담당자에게
   전달할 사실로 취급한다 — 더 우회하려 하지 않는다.

## Windows에서 Python 스크립트로 이 방식을 쓸 때 흔한 함정

- `python3`가 여러 개 설치돼 있으면(Windows Store 버전, `pythoncore-*` 등) `pip install`이 설치한
  위치와 스크립트를 실제로 실행하는 `python3`가 다를 수 있다 — `ModuleNotFoundError`가 나면
  `python3 -c "import sys; print(sys.executable)"`로 실제 실행 경로를 확인하고, 그 정확한 실행
  파일로 `-m pip install`과 스크립트 실행을 **둘 다** 맞춰야 한다.
- 한글이 포함된 응답을 `print()`로 파일에 리다이렉트하면 Windows 콘솔 코드페이지(cp949 등)로
  깨져서 저장될 수 있다 — 실행 전에 `PYTHONIOENCODING=utf-8`을 지정할 것.

Source: mid `plans/chatbot/progress-log.md` (LEARNING, 2026-09-11 — PDA agent 연결 확인 작업 중
dev/prod 서비스 계정 신원 확인 과정에서 반복 재현)
