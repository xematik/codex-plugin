# Xematik diagram review for Codex

Install the marketplace and plugin in a terminal:

```sh
codex plugin marketplace add xematik/codex-plugin
codex plugin add xematik-diagram-review@xematik
```

Download your platform's `xematik-channel` binary and `SHA256SUMS` from [releases](https://github.com/xematik/codex-plugin/releases). The binary supports Linux and macOS amd64/arm64 and Windows amd64. On Linux check with `sha256sum -c SHA256SUMS --ignore-missing`; on macOS use `shasum -a 256 -c SHA256SUMS --ignore-missing`. Put the executable on your PATH as `xematik-channel` (`xematik-channel.exe` on Windows).

For Linux x86-64, run these commands from the directory containing the downloaded files:

```sh
sha256sum -c SHA256SUMS --ignore-missing
mkdir -p ~/.local/bin
chmod +x xematik-channel_linux_amd64
mv xematik-channel_linux_amd64 ~/.local/bin/xematik-channel
export PATH="$HOME/.local/bin:$PATH"
```

Continue only if checksum verification reports `xematik-channel_linux_amd64: OK`.

1. Run `xematik-channel login` and open the link it prints.
2. For Bash, add the following line to `~/.bashrc` so future terminals can find the bridge:

   ```sh
   export PATH="$HOME/.local/bin:$PATH"
   ```

3. Restart Codex and sign in to Xematik when prompted.
4. Ask Codex: “Use the Xematik setup skill, then open a diagram review.”

For another Xematik server, clone this repository, change the URL in `mcp.json` to your server's `/mcp` endpoint, then add that local directory with `codex plugin marketplace add ./codex-plugin` and install the plugin using the command above. Set `XEMATIK_URL` to the server origin in the environment before running the bridge. Do not install the public marketplace as well under the same name.

## Feedback and recovery

After opening a review, the review skill uses the current `CODEX_THREAD_ID` and runs `xematik-channel codex-watch <session_id> --thread "$CODEX_THREAD_ID" --after <last_handled_revision>` under an independent supervisor. For example, on Unix hosts that allow detached processes to survive the agent turn:

```sh
nohup xematik-channel codex-watch <session_id> --thread "$CODEX_THREAD_ID" --after <last_handled_revision> > codex-watch.log 2>&1 </dev/null &
xematik-channel codex-status <session_id>
```

Check `codex-watch.log` for errors. Some sandboxes terminate background processes when a turn ends; use a host supervisor in that case. If no durable supervisor or thread ID is available, poll `await_feedback`. Use `xematik-channel codex-status <session_id>` to inspect, and `xematik-channel codex-stop <session_id>` to stop. Restart the watcher with the same binding to resume pending work. Notifications are at least once after an ambiguous crash. For `wake_failed`, inspect the thread and acknowledge the delivered revision using `xematik-channel codex-ack <session_id> --revision N`. For `uncertain`, inspect whether it was queued: acknowledge if received, or run `xematik-channel codex-retry <session_id> --revision N` if not, then restart. Do not retry an accepted signal blindly.
