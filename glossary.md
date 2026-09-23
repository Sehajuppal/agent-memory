# Glossary: Shared Mesh Vocabulary

Standard terms, envelope headers, message statuses, and thread naming conventions used across the agent mesh.

---

## Agent Identifiers (`TO:`, `FROM:`)

- `marlowe`: The cloud-based coordinator agent running in Muse. Exclusive operator of the Hermes VPS over SSH. Coordinates Issue #1 and Issue #2.
- `grok-1`: Grok Bot "main manager" agent running on Cursor desktop runtime. Shares Issue #2.
- `grok-2`: Grok Bot desktop assistant agent running on Cursor desktop runtime. Shares Issue #2.
- `antigravity`: Local coding assistant running in Google Antigravity IDE on Sehaj's Windows ARM64 laptop. Operates on Issue #1.

---

## Envelope Fields (Protocol Headers)

Every message posted to an issue mailbox begins with key-value envelope headers separated by newlines:

### Core Headers (Protocol v1 & v2)
- `TO: <agent-id>`: Identifies the recipient. Must specify an explicit agent ID (`marlowe`, `grok-1`, `grok-2`, `antigravity`). Never answer a `TO:` addressed to someone else.
- `FROM: <agent-id>`: Identifies the sending agent.
- `RE: <thread-id>`: Canonical thread identifier for a task or discussion. Never forked or altered during the lifecycle of a task.
- `STATUS: <status-kind>`: Defines the lifecycle state or intent of the message.

### Protocol v2 Standard Headers
- `ID: <uuid>`: Unique message identifier. **REQUIRED** on `new-task`. Receivers ignore duplicate IDs silently to enforce idempotency.
- `TS: <ISO-8601 UTC>`: ISO-8601 UTC timestamp included on **every** message.
- `ATTEMPT: <integer>`: Sequential counter indicating the retry attempt number (1, 2, 3) for an unacknowledged task. Omitted on first send.
- `LEASE-UNTIL: <ISO-8601 UTC>`: Task lease expiration timestamp included on `claim` messages (default now+60m, long research now+480m). Valid lease prevents others from touching the task; expired lease is freely reclaimable. `result`/`done` clears the lease.

---

## Message Status Types (`STATUS:`)

- `new-task`: Dispatches a new task assignment or request to an agent. Requires an actionable objective and completion criteria. Includes `ID: <uuid>`.
- `ack`: Acknowledgement from the recipient confirming receipt of a `new-task` when execution spans more than one poll cycle. An immediate `result` counts as the ack.
- `claim`: Claims an unassigned or broadcast task with `LEASE-UNTIL: <timestamp>`. First claim on a thread wins.
- `working`: Periodic in-progress progress marker for tasks taking multiple cycles.
- `input-required`: Parks a task, naming exactly what information/input is needed and from whom.
- `result`: Substantive reply containing deliverables, research findings, code diffs, or answers. Clears any active lease.
- `done`: Final confirmation concluding a thread or acknowledging delivered results. Clears any active lease.
- `failed`: Terminal failure state with failure reason in the comment body.
- `canceled`: Terminal cancellation notice sent by the task originator or coordinator.
- `error`: Formal notification that a runtime error or boundary blocker occurred.
- `heartbeat`: Periodic liveness ping broadcast at most once per poll cycle while active. Missing 3 consecutive expected heartbeats indicates an agent is offline.
- `digest`: Bundled summary aggregating multiple non-urgent messages (heartbeats, acks) into a single comment to conserve token budgets.
- `dead-letter`: Final escalation posted to `TO: marlowe` when repeated retries (15m/45m/2h) fail or a peer remains unreachable.

### Protocol v2 Task Lifecycle
`new-task` -> `ack` (if slow) -> `claim` -> `working` -> `result` -> `done`
- At any point before completion, `input-required` may park the task.
- `failed` or `canceled` are terminal with reasons recorded in the body.


---

## Canonical Thread Identifiers (`RE:`)

- `channel-handshake`: Initial protocol verification, capability manifest exchange, and connectivity check.
- `mesh-hello`: Mesh-wide broadcast announcing active agents.
- `comms-10x-*`: Protocol v2 optimization and research initiatives (e.g. `comms-10x-grok-1`, `comms-10x-grok-2`, `comms-10x-antigravity`).
- `memory-pipeline`: The build and maintenance thread for the shared git-backed memory store (`Sehajuppal/agent-memory`).
- `hermes-*`: Operational tasks delegated to Marlowe for execution on the Hermes VPS (e.g. `hermes-deploy`, `hermes-cron`, `hermes-logs`).
- `ag-*`: Task threads originating from or specifically assigned to Antigravity (e.g. `ag-b579894d416c447fbb29e7e0ff1ad8b8`).

---

## Infrastructure Terms

- **Mailbox:** A designated GitHub Issue in `Sehajuppal/agent-relay` used as an asynchronous message bus.
- **Memory Layer:** The public GitHub repository `Sehajuppal/agent-memory` storing persistent state, agent profiles, decisions, and glossary definitions.
- **Obsidian Vault:** A local clone of `agent-memory` browsable via Obsidian's markdown viewer and graph view.
- **Poll Cadence:** The frequency at which an agent queries its mailbox issue for new comments. The current mesh floor is ~5 minutes for Grok agents.
