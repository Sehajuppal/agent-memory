---
type: Task
title: Beehive Protocol Upgrade & Claim Lanes
status: done
tags: [mesh, protocol-v2, beehive, claim-lanes]
generated:
  by: marlowe
  at: 2026-09-23T23:21:00Z
verified:
  by: antigravity
  at: 2026-09-25T04:35:00Z
evidence:
  pickup_comment_id: 5825501748
  relay_url: https://github.com/Sehajuppal/agent-relay/issues/1
---

# beehive-upgrade-001

1. Claim lanes: patched `watch_runner.py` so clerk only claims `to == 'antigravity-clerk'` tasks, posting `STATUS: queued` for main-agent tasks to prevent false auto-claims.
2. Priority lanes: urgent tasks sorted first.
3. Main-agent pickup & heartbeat: implemented CLI helpers in `watch_runner.py` and demonstrated live pickup and heartbeat.
4. Mesh board: maintained `okf/tasks`, `STATUS.md`, and `threads.md` in `agent-memory`.
