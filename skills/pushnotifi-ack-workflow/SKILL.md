---
name: pushnotifi-ack-workflow
description: Human-in-the-loop approvals on the phone via PushNotifi MCP. Use when the user needs production-write gates, ambiguous decisions, or explicit human ack before the agent continues. Covers pushnotifi_request_ack, pushnotifi_await_ack, response_template values, and timeout safety.
---

# PushNotifi approval workflow (MCP)

This plugin exposes PushNotifi tools on the `pushnotifi` MCP server. Prefer them over inventing HTTP when the user wants phone-based approval.

## Core loop

1. **`pushnotifi_request_ack`** — sends an emergency-priority notification. Returns a `correlation_id` (server `alert_id`). Optional `response_template` controls buttons on the device.

2. **`pushnotifi_await_ack`** — polls until the user responds or `timeout_seconds` elapses. Returns `{ acked: true, ... }` or `{ acked: false, reason: 'timeout' }`.

Always call `request_ack` before `await_ack` with the returned `correlation_id`. Do not reuse stale IDs.

## `response_template` values

| Value | UI |
| ----- | --- |
| omitted or `ack` | Acknowledge |
| `yes_no` | Yes / No |
| `approve_deny` | Approve / Deny |
| `proceed_abort` | Proceed / Abort |
| `confirm_cancel` | Confirm / Cancel |
| `freetext` | Typed reply on the message detail screen |

## Safety expectations

- The user may dismiss the notification without approving. Treat **no tap** as **no consent**.
- Use a **short** `timeout_seconds` for destructive actions (for example ≤ 300s for production writes).
- Phrase the message so the safe default is inaction; only a deliberate tap should authorize risk.

## Other tools on the same server

- `pushnotifi_send`, `pushnotifi_list_messages`, `pushnotifi_list_groups`, `pushnotifi_test` — operational sends and discovery.

Credentials come from plugin configuration at enable time, not from tool arguments.
