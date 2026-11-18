---
name: diagram-review
description: Review and edit code architecture diagrams with the user in Xematik.
---

Call `get_agent_guide` first. Build the diagram from real code with `@level` and `@code` anchors; call `open_review` and give the user its URL. Use the current `CODEX_THREAD_ID` as the exact thread UUID. Use the existing bridge login; if login is needed, run `xematik-channel login`. Start `xematik-channel codex-watch <session_id> --thread "$CODEX_THREAD_ID" --after <last_handled_revision>` under a supervisor that survives the turn. If the thread ID or durable supervisor is unavailable, poll `await_feedback` instead. Inspect with `xematik-channel codex-status <session_id>` and stop with `xematik-channel codex-stop <session_id>`.

On a wakeup call `await_feedback` for that revision; on waiting call it again, and on closed stop. For Discuss or Plan, answer with `reply` and do not edit code. For Apply, edit code with existing permissions and verification, then call `push_revision`. Never delete code without asking. Call `close_review` when done. For recovery and ambiguous deliveries see the [package README](../../README.md).

## Sharing and blocked replies

The user decides which repository information to share. Honor explicit authorization
to share project diagrams, findings, and plans with Xematik in the selected review.
Keep that authorization scoped to the requested content and destination. Do not infer
permission to share secrets, credentials, unrelated files, or other people's data.
Do not ask again when the user has already authorized the same sharing.

Host approval controls still apply. If a tool rejects a detailed reply, send a short
acknowledgment without repository details when permitted. Explain the block in the
local conversation. Do not leave feedback unanswered or bypass the rejection through
another tool. Use existing explicit authorization as evidence when review is supported;
otherwise ask only for the missing authorization.
