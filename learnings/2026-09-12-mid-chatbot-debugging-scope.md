# MID chatbot debugging scope control

- Date: 2026-09-12
- From: mid chatbot incident debugging around `/v1/suggestions`
- Status: learning, not a rule yet

## Learning

When debugging MID chatbot endpoint failures, keep code changes scoped to
chatbot-owned files unless the user explicitly asks to broaden the fix.

For `/v1/suggestions`, the null-menu failure belonged in
`ChatbotAgentMenuGuard`: a `MenuDto` or `MenuDto.pageUrl` can be null, and
`List.of(...).contains(null)` throws a `NullPointerException`. The local fix is
to skip null menu rows and null page URLs before checking required chatbot menu
paths.

Do not modify stable shared or unrelated MID areas while handling chatbot
incidents unless explicitly requested, including:

- `CommonInterceptor`
- `/common/*`
- `/secsso/*`
- datasource or application profile config
- Paid Digital Media controllers/services

For adjacent logs from those areas, diagnose and report the likely cause only.

## Repro / when it matters

This matters when a session includes several nearby backend errors, such as
chatbot, PDM, common metadata, and SSO refresh logs. Similar stack shapes do not
make them the same ownership area.

Before editing, classify the target file as one of:

- chatbot-owned
- shared infrastructure
- unrelated feature area

Only edit the first category by default during chatbot debugging.

## API smoke note

Shell calls to MID chatbot endpoints can return `401` because browser access
tokens are unavailable. Direct Cloud Run agent calls can return `403` without a
Google ID token. Treat these as authentication/platform reachability results,
not as proof of agent API contract behavior.

## Do not promote if

Do not promote this as a general rule for every repo. The file names, endpoint
paths, and ownership boundaries are specific to MID.
