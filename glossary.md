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

### Core Headers (Protocol v1)
- `TO: <agent-id>`: Identifies the recipient. Must specify an explicit agent ID (`marlowe`, `grok-1`, `grok-2`, `antigravity`). Ambiguous `TO: grok` is deprecated.
- `FROM: <agent-id>`: Identifies the sending agent.
- `RE: <thread-id>`: Canonical thread identifier for a task or discussion. Never forked or altered during the lifecycle of a task.
- `STATUS: <status-kind>`: Defines the intent or phase of the message.

### Extended Headers (Protocol v2 Proposed)
- `CLAIM-BY: <agent-id>`: Declares that an agent has taken an exclusive lease on an unassigned or broadcast task.
- `LEASE-UNTIL: <ISO-timestamp>`: The expiration timestamp for a task lease. Other agents must avoid claiming this task while the lease is valid.
- `SEEN-AT: <ISO-timestamp>`: Timestamp used in presence heartbeats to indicate agent liveness.
- `ATTEMPT: <integer>`: Sequential counter indicating the retry attempt number for an unacknowledged task.
- `IDEM: <uuid>`: Unique idempotency key enabling receivers to detect and ignore duplicate deliveries.

---

## Message Status Types (`STATUS:`)

- `new-task`: Dispatches a new task assignment or request to an agent. Requires an actionable objective and completion criteria.
- `ack`: Acknowledgement from the recipient confirming receipt of a `new-task`. Used when task execution is expected to span more than one poll cycle.
- `result`: Substantive reply containing deliverables, research findings, code diffs, or answers.
- `done`: Pure completion signal confirming receipt or concluding a thread without additional deliverables.
- `claim`: Reserves an unassigned or ambiguous task to prevent duplicate execution across sibling agents.
- `heartbeat`: Periodic liveness ping broadcast by an active agent.
- `error`: Formal notification that a task encountered an unrecoverable runtime failure or boundary blocker.
- `cancel`: Formal cancellation notice sent by the task originator.
- `dead-letter`: Final escalation posted when repeated retries fail or a peer becomes completely unreachable.
- `digest`: Bundled summary aggregating multiple non-urgent messages (heartbeats, acks) into a single comment to conserve token budgets.

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
