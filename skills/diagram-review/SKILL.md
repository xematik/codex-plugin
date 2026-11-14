---
name: diagram-review
description: Review and edit code architecture diagrams with the user in Xematik.
---

Call `get_agent_guide` first. Build the diagram from real code with `@level` and `@code` anchors; call `open_review` and give the user its URL. Use the current `CODEX_THREAD_ID` as the exact thread UUID. Use the existing bridge login; if login is needed, run `xematik-channel login`. Start `xematik-channel codex-watch <session_id> --thread "$CODEX_THREAD_ID" --after <last_handled_revision>` under a supervisor that survives the turn. If the thread ID or durable supervisor is unavailable, poll `await_feedback` instead. Inspect with `xematik-channel codex-status <session_id>` and stop with `xematik-channel codex-stop <session_id>`.

On a wakeup call `await_feedback` for that revision; on waiting call it again, and on closed stop. For Discuss or Plan, answer with `reply` and do not edit code. For Apply, edit code with existing permissions and verification, then call `push_revision`. Never delete code without asking. Call `close_review` when done. For recovery and ambiguous deliveries see the [package README](../../README.md).
