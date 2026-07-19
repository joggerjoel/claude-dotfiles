---
name: headroom
description: Start, stop, restart, or check the Headroom proxy (context-optimization layer on port 8787). Usage - /headroom [start|stop|restart|status|token|cache]. Default is status.
disable-model-invocation: true
---

Manage the local Headroom proxy. The binary may not be on PATH in this
shell — resolve it first:

```bash
HR="$(command -v headroom || true)"; [ -z "$HR" ] && HR="$HOME/.local/bin/headroom"
```

Parse the argument (default: `status`). If a persistent deployment exists
(`"$HR" install status` succeeds without the "No deployment profile" error),
prefer `"$HR" install start|stop|restart|status` for everything and skip the
manual steps below.

## status (default)

1. Run `"$HR" doctor` and show the table.
2. `curl -m 3 -fsS http://127.0.0.1:8787/health` — report version, and pull
   `mode` from `curl -s http://127.0.0.1:8787/stats` (`summary.mode`).
3. One-line verdict: proxy up/down, which tools are routed, current mode.

## start [token|cache]

1. If `/livez` already answers, say so and stop — don't double-start.
2. Start detached (survives the session; optional mode arg sets
   `HEADROOM_MODE`, otherwise the version default `cache` applies):
   ```bash
   (HEADROOM_MODE=<mode-if-given> nohup "$HR" proxy >/dev/null 2>&1 &)
   ```
3. Poll `/livez` (up to ~10s). Confirm with version + mode from `/health`
   and `/stats`. If it doesn't come up, run `"$HR" proxy` in the background
   via the Bash tool to capture the startup error, and report it.
4. Remind: manual starts die on reboot; `headroom install apply` makes it
   persistent.

## stop

1. Find the listener: `lsof -nP -iTCP:8787 -sTCP:LISTEN` (fallback port from
   `$HEADROOM_PORT`).
2. Verify the PID's command line is headroom/python (`ps -p <pid> -o command=`)
   before killing — never kill an unrelated process squatting on the port.
3. `kill <pid>`, wait ~2s, confirm `/livez` no longer answers (escalate to
   `kill -9` only if still alive after 5s).
4. Warn: any tool still routed through the proxy (check `"$HR" doctor`) will
   fail until the proxy is started again or the tool is unwrapped.

## restart [token|cache]

Stop, then start (preserve the mode argument). Used to pick up a new
headroom version — mention the running version before and after.
