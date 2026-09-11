---
name: slice-review-checklist
description: >-
  Review a slice using diff only: pass, partial pass, or reject with a fix list.
  Use when the user asks for 검수, review, 통과, 반려, or "/slice-review".
user-invocable: true
---

# Slice review checklist

재구현하지 않는다. diff와 핸드오프·규칙만 본다.

## Steps

1. **시작**
   - `session-start-check` 스텝을 이미 안 했으면 먼저 수행한다.
   - 핸드오프의 완료 조건 · break-list · `건드릴 파일` 을 읽는다.

2. **범위**
   - `git diff`(및 스테이징)만 본다. 코드를 새로 짜거나 큰 리팩터 제안으로 대체하지 않는다.
   - `건드릴 파일` 밖 변경이 있으면 **반려** 후보.

3. **렌즈 (고정)**
   - 완료 조건 충족 여부
   - break-list 항목
   - 건드릴 파일 밖 수정
   - 민감정보 기입 (rules/no-sensitive-in-files.md)
   - 앱 AGENTS / 협업 문서와 모순
   - 앱 전용 보안·계약 세부는 **앱 문서에 있을 때만** 그 목록을 따른다. 없으면 지어내지 않는다.

4. **판정 한 줄**
   - `통과:` 완료 조건 충족. (선택) 근거 파일 경로.
   - `부분통과:` 남은 수정 목록 N건 — 각 줄에 파일 경로 + 기대 동작.
   - `반려:` 치명 사유 한 줄 (건드릴 파일 위반 / 완료 조건 미충족 / 민감정보 등).
   - 규칙 인용: `rules/<file>` 또는 `<APP>/AGENTS.md` / 핸드오프 항목명.
   - harness 규칙과 앱이 겹치면 **앱 우선**. 충돌 시 사유에 `앱 우선, harness는 참고` 를 쓰고 harness 파일을 고치지 않는다.

5. **harness**
   - 기본: harness 편집 안 함.
   - 팀 기본으로 올릴 만하면 `승격 후보: …` 한 줄만. `promote-learning` 은 사용자가 시킬 때만.

## Done when

`통과|부분통과|반려` 한 줄 + (필요 시) 수정 목록을 남기고 멈춘 상태. 그 블록은 종결이다.
