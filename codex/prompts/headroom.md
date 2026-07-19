Manage the local Headroom proxy (context-optimization layer this Codex
session routes through on port 8787). Argument: $ARGUMENTS — one of
start | stop | restart | status | token | cache. Default: status.

The headroom binary may not be on PATH: use "$HOME/.local/bin/headroom"
if `command -v headroom` fails.

If a persistent deployment exists (`headroom install status` succeeds
without a "No deployment profile" error), use
`headroom install start|stop|restart|status` and skip the manual steps.

- status: run `headroom doctor`; also `curl -m 3 -fsS
http://127.0.0.1:8787/health` for version and
  `curl -s http://127.0.0.1:8787/stats` for summary.mode. Give a
  one-line verdict: up/down, routed tools, mode.
- start [token|cache]: if /livez already answers, say so and stop.
  Otherwise start detached:
  `(HEADROOM_MODE=<mode-if-given> nohup "$HR" proxy >/dev/null 2>&1 &)`
  then poll /livez up to 10s and confirm version + mode. Note that a
  manual start dies on reboot.
- stop: find the PID with `lsof -nP -iTCP:8787 -sTCP:LISTEN`, verify
  via `ps -p <pid> -o command=` that it is headroom/python before
  killing, then `kill`, confirm /livez stops answering. WARNING: this
  Codex session itself routes through the proxy — after stopping,
  requests will fail until it is started again or codex is unwrapped
  (`headroom unwrap codex`). Say this clearly before killing.
- restart [token|cache]: stop then start, reporting version before and
  after.
