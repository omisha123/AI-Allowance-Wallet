# Demo script (target: 2:50, hard limit 3:00)

Use fictional names only: StreamA, StreamB, StreamC. No real logos, no copyrighted music.
Say once, honestly: "Viewing data and the catalog are sample data for this demo."

## Seed data (everyone builds against this)
- Policy: monthly 30.00, daily 15.00, per-action 12.00, approval threshold 5.00.
- Wallet: **20.00, pre-set by `POST /demo/reset`** (no PayPal Order behind it). Real top-ups still work through `POST /wallet/fund` and capture; they are built, but not required in the video.
- Platforms: StreamA 7.99 (Series A), StreamB 4.99 (Series B), StreamC 5.99 (Movie C).
- Plan result for "Series A, Series B, Movie C": 3 months, total 18.97 vs 56.91 if all three ran for 3 months, saving 37.94.
- Merchants: IndexedPapersAPI (api_call, 0.05), ComputeNodeA (compute, 0.10), DataVaultDatasets (dataset, 20.00).
- All sandbox payouts go to `PAYOUT_RECEIVER_EMAIL` unless a platform/merchant has its own `receiver_email`.

**Before recording:** hit `POST /demo/reset`. Check the PayPal sandbox **Business** account has enough balance (Payouts are paid from it). Dashboard and agent chat open.

| Time | Say / do | Shows |
|---|---|---|
| 0:00 | "We all pay for streaming platforms we barely use, and AI agents are starting to spend money too. Nobody gives either of them a budget." | Problem |
| 0:20 | Dashboard: wallet 20.00, policy card. Say: "The demo starts with a seeded 20.00 so every run is the same. Real top-ups go through a PayPal sandbox Order." Optional, only if time: click Add funds and show the sandbox approve page for 3 seconds. | One wallet, rules first |
| 0:40 | Type: "I want to watch Series A, then Series B, then Movie C." | Natural language goal |
| 1:00 | Timeline appears: Month 1 StreamA, Month 2 StreamB, Month 3 StreamC. Point at 18.97 vs 56.91 and the 37.94 saved. | AI planner + value |
| 1:20 | Click pay on Month 1 (7.99). Over the 5.00 threshold, so it shows PENDING_APPROVAL. Click Approve. | Approval flow, human in control |
| 1:40 | Status flips to SETTLED. Show the PayPal sandbox Payout / transaction. | Real PayPal money movement |
| 1:55 | Switch to the agent chat: "Find me an academic paper on X." Agent calls `check_budget`, then `request_purchase` to IndexedPapersAPI for 0.05. Decision: ALLOW, logged in the ledger. | MCP tools + micro-purchase |
| 2:15 | Agent then tries a 20.00 dataset from DataVaultDatasets. Decision: BLOCK (per-action cap), with the reason shown. | Guardrails work |
| 2:30 | Audit log: who spent (user / agent / timeline), what, why. Click "Run settlement" to batch the micro-purchases into one payout. | Audit + batched settlement |
| 2:45 | "One wallet. Agents plan and buy. Policies protect. Humans approve. PayPal moves the money." | Close |

**Backup:** record a full clean take the day before the deadline. If live PayPal misbehaves on the day, use that take.
