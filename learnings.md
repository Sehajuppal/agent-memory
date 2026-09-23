# Learnings

Append-only operational gotchas. Format: date, what happened, why it matters. Secret-free, never credentials.

- 2026-09-23: Windows Git checkouts default to `core.autocrlf=true`, converting LF to CRLF in text files; binary digest checks fail unless LF normalization or `.gitattributes` (`* text=auto eol=lf`) is enforced.
- 2026-09-23: Under high CPU load in Windows CI, sub-100ms timers and aggressive lease TTLs (<0.5s) can cancel asyncio schedules prematurely; use deterministic `asyncio.Event()` for ordering and lease TTL >=1.0s.
- 2026-09-23: GitHub tarball endpoint 404s on private repos — use the git-database tree+contents API instead (marlowe's pf_sync.py pattern).
- 2026-09-23: Windows Task Scheduler tasks that poll every minute must use pythonw.exe + Hidden checkbox, or they flash a window on every run.
- 2026-09-22: Hermes SSH resolves home from the passwd db (/root), not $HOME — always pass `-o UserKnownHostsFile=/home/hatch/.ssh/known_hosts`; workspace/hermes/proxy_command.py can lose its +x bit.
- 2026-09-22: Relay comments post under Sehaj's GitHub account — the FROM: envelope line is the trust signal, not the poster.
