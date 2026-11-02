# MCP tools (agent -> backend)

Two tools. Same pool of money as the subscription timeline.
**The agent can never approve a purchase.** Approval is a human action in the dashboard.

## `check_budget`
Calls `GET /budget`. No input.

```json
{
  "name": "check_budget",
  "description": "Check how much the agent can spend right now under the user's policy. Call before requesting a purchase.",
  "input_schema": { "type": "object", "properties": {}, "required": [] }
}
```

Returns `available`, `remaining_today`, `remaining_month`, `per_action_cap`, `approval_threshold`.

## `request_purchase`
Calls `POST /purchase-request` with `requester: "agent"`.

```json
{
  "name": "request_purchase",
  "description": "Ask to buy something (paid API call, compute, dataset). The policy engine will ALLOW, REQUIRE_APPROVAL (a human decides), or BLOCK. Always explain why the purchase is needed.",
  "input_schema": {
    "type": "object",
    "properties": {
      "category": { "type": "string", "enum": ["api_call", "compute", "dataset"] },
      "merchant": { "type": "string" },
      "description": { "type": "string" },
      "amount": { "type": "string", "description": "USD with 2 decimals, e.g. \"0.05\"" },
      "reason": { "type": "string", "description": "Why this purchase helps the user's task" }
    },
    "required": ["category", "merchant", "description", "amount", "reason"]
  }
}
```

Returns the transaction: `decision.decision`, `decision.reason`, `status`.

## What the MCP server adds (the model does not send these)
- `requester: "agent"` and `currency: "USD"` are added by the MCP server before it calls `POST /purchase-request`.
- At startup the agent service calls `GET /merchants` and puts the list in the agent's system prompt as "merchants you may buy from".

## Agent rules (put these in the system prompt)
1. Call `check_budget` before any purchase request.
2. If the result is BLOCK, tell the user why. **Never split a purchase into smaller ones to get around a cap.**
3. If the result is REQUIRE_APPROVAL, say a human has to approve and carry on with other work.
4. Always give a real `reason`. It appears in the audit log.
5. Never invent merchants. Only use merchants from the injected `/merchants` list. If none fits, say so instead of guessing (the backend returns 422 unknown_merchant anyway).

## Planner (separate from the MCP tools)
The subscription planner reads `GET /catalog`, takes the typed titles, and returns the `Plan` object from the contract (`POST /plan`). It must only use platforms and titles that exist in the catalog, and order months so no two platforms overlap.
