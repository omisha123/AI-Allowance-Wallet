# Team rules (hackathon deadline: Nov 12, 2026, 12:00pm PST = about 1:30am IST on Nov 13)

**Submit by the evening of Nov 11 IST.** Never leave the upload for the last night.

## Rules
- Never change `docs/openapi.yaml` without telling everyone. Open a PR, tag the team, wait for a thumbs up.
- Small PRs, merge to `main` daily.
- 10-minute standup daily: what's done, what's blocked.
- Frontend builds against mocks of the contract (`npx @stoplight/prism-cli mock docs/openapi.yaml` (test it once on day 1)). Agent tests against the same mock until the backend is ready.
- Never commit `.env` or PayPal credentials.
- Anything not in `docs/demo-script.md` gets cut.

## Roles
1. Payments/backend: PayPal Orders + Payouts, wallet ledger, policy engine
2. AI/agent: subscription planner, MCP server (`check_budget`, `request_purchase`)
3. Frontend/design: dashboard, timeline, approvals, audit log
4. Product/DevOps/demo: repo, deployment (Render), docs, video, Devpost submission

## Checkpoints
- Days 1-2: contract, demo script, sandbox accounts, Orders + Payouts test calls
- Day 3 go/no-go: one flow works end to end locally (request -> policy -> PayPal sandbox payment -> dashboard shows it). If not, cut scope (e.g. drop the planner, keep the agent purchase flow)
- Days 6-8: full integration, first Render deploy
- Day 9: feature freeze
- Day 11: final deploy, README tested by someone outside the team, rehearsed recording
- Day 12: buffer
