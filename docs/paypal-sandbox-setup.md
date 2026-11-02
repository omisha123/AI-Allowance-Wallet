# PayPal sandbox setup + Day 1 test

Backend lead does this on Day 1-2. The plan's biggest risk is Payouts being blocked in sandbox, so find out early.

## Setup
1. Log in at developer.paypal.com, open **Apps & Credentials**, switch to **Sandbox**.
2. Under **Testing Tools -> Sandbox Accounts**, you need:
   - one **Business** account (the wallet owner / sender)
   - one **Personal** account (the buyer who tops up the wallet via Orders)
   - one or two more **Personal** accounts (receivers of payouts, e.g. a "platform" account)
   Payouts are paid from the Business account balance, so check it has funds. Put one Personal receiver's email in `PAYOUT_RECEIVER_EMAIL` in `.env`.
3. Create an app. Copy **Client ID** and **Secret** into your local `.env`. Never commit it.

## Test 1: token
```bash
curl -s -X POST https://api-m.sandbox.paypal.com/v1/oauth2/token \
  -u "$PAYPAL_CLIENT_ID:$PAYPAL_CLIENT_SECRET" \
  -d "grant_type=client_credentials"
```
Put the `access_token` in `ACCESS_TOKEN`.

## Test 2: Orders (funds the wallet)
```bash
curl -s -X POST https://api-m.sandbox.paypal.com/v2/checkout/orders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -d '{
    "intent": "CAPTURE",
    "purchase_units": [{ "amount": { "currency_code": "USD", "value": "20.00" } }]
  }'
```
Open the `approve` (or `payer-action`) link from the response, log in with the sandbox **Personal** buyer account and approve. Then capture:
```bash
curl -s -X POST https://api-m.sandbox.paypal.com/v2/checkout/orders/$ORDER_ID/capture \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN"
```
Success = `"status": "COMPLETED"`.

## Test 3: Payouts (pays a purchase)
```bash
curl -s -X POST https://api-m.sandbox.paypal.com/v1/payments/payouts \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer $ACCESS_TOKEN" \
  -d '{
    "sender_batch_header": {
      "sender_batch_id": "test-'"$(date +%s)"'",
      "email_subject": "Test payout"
    },
    "items": [{
      "recipient_type": "EMAIL",
      "amount": { "value": "1.00", "currency": "USD" },
      "receiver": "YOUR-PAYOUT_RECEIVER_EMAIL-VALUE",
      "note": "Day 1 test",
      "sender_item_id": "item-1"
    }]
  }'
```
Success = response contains `batch_header.payout_batch_id`. Check the receiver's sandbox account for the 1.00.

## Decision after the tests
- Orders works, Payouts works: build as planned (`SETTLEMENT_MODE=payouts`).
- Orders works, Payouts blocked: write down the exact error, tell the team the same day, switch to **Orders + internal ledger** (`SETTLEMENT_MODE=ledger_only`). The contract does not change; only what settlement does behind the scenes.
- Orders also fails: stop and tell everyone immediately, this changes the plan.
