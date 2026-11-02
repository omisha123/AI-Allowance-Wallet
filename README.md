# AI Allowance Wallet

An AI-powered allowance for your digital life. It plans your streaming subscriptions so you never pay for more than one platform at a time, and lets your AI agents buy what they need (paid APIs, compute, datasets) within limits you control. One wallet, one policy engine, one audit log, settled through PayPal.

Built for the PayPal AI Hackathon, using the PayPal sandbox.

## How it works
1. Fund the wallet through a PayPal sandbox Order. (The demo reset pre-sets 20.00 with no Order, so every run starts the same.)
2. Set a policy: monthly budget, daily cap, per-action cap, approval threshold.
3. Tell the AI what you want to watch. It builds a month-by-month subscription timeline.
4. Each step is paid from the wallet only when the timeline says so. Anything over the approval threshold waits for you.
5. Your AI agent can request purchases through MCP tools (`check_budget`, `request_purchase`). The policy engine allows, asks you, or blocks.
6. Everything lands in one audit log. Small purchases are batched into one PayPal Payout.

## Honest notes
- Viewing data and the platform catalog are **sample data** (5-6 fictional platforms).
- Real autopay cancellation is out of scope. The wallet model pays each platform only when the plan says so.
- Everything runs in the **PayPal sandbox**. No real money moves.
- In the demo, every sandbox payout goes to one receiver account (`PAYOUT_RECEIVER_EMAIL`).

## Repo layout
```
backend/    API, wallet ledger, policy engine, PayPal client
agent/      subscription planner + MCP server (check_budget, request_purchase)
frontend/   dashboard: wallet, timeline, approvals, audit log
docs/       openapi.yaml (THE contract), mcp-tools.md, demo-script.md, paypal-sandbox-setup.md
```

## Run it
```bash
cp .env.example .env      # add PayPal sandbox + AI credentials
# Run commands for backend, agent and frontend go here (owners fill in)
# Then open the dashboard and click "Reset demo" to seed the data
```

Hosted demo: _add Render URL here_

## Tools used
- PayPal sandbox: Orders API (fund the wallet), Payouts API (settle purchases)
- Claude API with tool use, plus an MCP server: subscription planner and agent purchasing
- _add hosting, database and frontend stack here_

## License
MIT, see `LICENSE`.
