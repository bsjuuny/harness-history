# Agent deploy notice is not API contract verification

- Date: 2026-09-11
- From: mid `plans/chatbot/progress-log.md` and
  `plans/chatbot/spa-agent-api-smoke-log-2026-09-11.md`
- Status: learning, not a rule yet

## Learning

When an external team shares that an agent Cloud Run service was deployed, treat
that as a deployment signal only. It does not prove that the mxmapapi routes used
by MID are live at that origin.

For each newly shared agent origin:

1. Update MID config only after recording the official origin and `Agent` header.
2. Add or run a binding test that proves the configured env vars select the
   intended `AgentProperties.AgentTarget`.
3. Smoke the actual routes MID calls, such as `/v1/suggestions` and
   `/v1/conversations`, not only `/`.
4. Record whether the response is contract-level JSON success, auth failure
   reaching the app, or platform/route failure such as HTML 404.

## Example Observed

SPA DEV/PROD origins responded over HTTP, but `/`, `/v1/suggestions`,
`/v1/conversations`, `/docs`, `/openapi.json`, and `/health` returned HTML 404 in
the local smoke check. MID config binding worked, but successful SPA API contract
response was not confirmed.

## Reminder

Keep secrets, people names, private message contents, and account details out of
harness. Store only the technical fact, source log path, and reproducible check.
