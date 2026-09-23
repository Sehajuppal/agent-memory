# Decisions Log

Append-only record of architectural, protocol, and operational decisions agreed upon across the mesh.

**Rules for modifying this file:**
1. All entries must follow the format: Date, Decision, Who made it, Why.
2. Entries are strictly **append-only**. Never edit, reorder, or delete past entries.
3. Every entry should cite the corresponding mailbox issue and thread ID (`RE:`) where the consensus was reached.

---

## 2026-09-23: Transport Mechanism via GitHub Issue Comments
- **Decision:** Use asynchronous GitHub issue comments in `Sehajuppal/agent-relay` with RFC-style key-value envelope headers (`TO:`, `FROM:`, `RE:`, `STATUS:`) as the inter-agent transport layer.
- **Who made it:** Marlowe, Sehaj.
- **Why:** All mesh agents operate on disparate host environments (cloud container, Cursor instances, local ARM64 laptop) with no open inbound ports or static IPs. GitHub issues provide a neutral, authenticated, persistent, durable, and universally accessible message bus that requires only outbound HTTPS polling.
- **Reference:** `Sehajuppal/agent-relay` Issue #1 body and Issue #2 body.

---

## 2026-09-23: Mesh Roster Definition & Mailbox Channel Split
- **Decision:** Establish an initial mesh roster of four agents (`marlowe`, `grok-1`, `grok-2`, `antigravity`). Split traffic across two mailbox channels:
  - Issue #1: `marlowe` <-> `antigravity`
  - Issue #2: `marlowe` <-> `grok-1` / `grok-2`
- **Who made it:** Marlowe, Sehaj.
- **Why:** Prevents message noise and accidental double-handling between the local laptop development environment (`antigravity`) and the cloud bot assistants (`grok-1` and `grok-2`), while positioning `marlowe` as the cross-mesh coordinator.
- **Reference:** Issue #1 Comment [4] and Issue #2 Comments [4], [5].

---

## 2026-09-23: Hermes VPS Operational Authority Centralized to Marlowe
- **Decision:** Centralize all Hermes VPS SSH operations strictly to `marlowe`. `grok-1` explicitly amended its capability manifest to strike direct SSH access; both Grok agents must delegate Hermes tasks to Marlowe using `TO: marlowe` / `RE: hermes-<topic>` / `STATUS: new-task`.
- **Who made it:** Grok-1, Marlowe.
- **Why:** Distributing SSH keys or credentials across multiple bot environments introduces high security risk. Centralizing execution in Marlowe ensures a single point of operational accountability, clean audit trails, and avoidance of race conditions on the Hermes host.
- **Reference:** Issue #2 Comment [3] and Comment [4].

---

## 2026-09-23: Polling Interval Floor for Grok Agents
- **Decision:** The poll interval floor for `grok-1` and `grok-2` is fixed at ~5 minutes. The initial protocol expectation of 1-2 minute polling was formally rejected for the Grok runtimes.
- **Who made it:** Grok-1, Grok-2, accepted by Marlowe.
- **Why:** 5 minutes is the platform floor for reliable polling on the Grok Bot runtime.
- **Reference:** Issue #2 Comments [1], [2], [4], [5].

---

## 2026-09-23: Overnight Task Allocation & Antigravity Stand-down
- **Decision:** Antigravity was stood down from the overnight protocol research task (`comms-10x-antigravity`), and its brief was reassigned to `grok-1`.
- **Who made it:** Marlowe, Sehaj.
- **Why:** Antigravity runs on Sehaj's personal laptop, which is powered down / suspended overnight. Assigning overnight deadlines to a sleeping host causes pipeline blockage.
- **Reference:** Issue #1 Comment [7] and Issue #2 Comment [8].

---

## 2026-09-23: Decoupling Shared Memory from Mailbox Transport
- **Decision:** Create a dedicated public GitHub repository `Sehajuppal/agent-memory` to act as the shared persistent memory store, keeping the issue mailbox strictly as the ephemeral transport layer.
- **Who made it:** Sehaj, Marlowe, Antigravity.
- **Why:** Issue threads are prone to noise, lack structured search across historical topics, and suffer from pagination limits. A git-backed markdown repository provides full-text search (GitHub Code Search API and local ripgrep), Obsidian knowledge vault browsing, and distinct file ownership.
- **Reference:** Issue #1 Comment [8] (`RE: memory-pipeline`).

---

## 2026-09-23: Concurrency-Safe File Architecture in Agent Memory
- **Decision:** Agent profiles are partitioned into individual files (`agents/marlowe.md`, `agents/grok-1.md`, `agents/grok-2.md`, `agents/antigravity.md`), each edited exclusively by its owning agent.
- **Who made it:** Antigravity (implementing the memory-pipeline specification).
- **Why:** Autonomous agents committing to a single monolithic manifest file would experience frequent merge conflicts when updating status or capabilities simultaneously.
- **Reference:** `Sehajuppal/agent-memory` repository structure.

---

## 2026-09-23: Protocol v2 Memory-Layer Implementation Policy
- **Decision:** Established the memory repository structure immediately using the prompt specification and existing mailbox consensus, while Marlowe's consolidated Protocol v2 draft is pending publication.
- **Who made it:** Antigravity (following Prompt Step 1 guidance).
- **Why:** The memory-pipeline specification provided complete requirements for repository layout, agent files, append-only decisions, glossary, and search verification. Proceeding avoids blocking the mesh, and the memory layer can easily be updated by any agent when Protocol v2 is published.
- **Reference:** Issue #1 Comment [8] Step 1.

---

## 2026-09-23: Standing Mailbox Watcher Global Skill
- **Decision:** Installed persistent global skill `mailbox-watch` at `C:\Users\sehaj\.gemini\config\skills\mailbox-watch\SKILL.md` with state tracking at `~/.gemini/agent-relay-state.json`.
- **Who made it:** Marlowe (requested), Sehaj (approved), Antigravity (installed).
- **Why:** Enables Antigravity to autonomously poll `Sehajuppal/agent-relay` Issue #1 on session start and periodically while online, tracking processed comments and preventing missed tasks.
- **Reference:** Issue #1 Comment [12] (`RE: mailbox-watch-skill`).

---

## 2026-09-23: Deterministic Oracles and Semantic Invariants in Pipeline Builder
- **Decision:** Enforced deterministic non-LLM oracles in quality audits, cross-artifact semantic consistency checking across serialized writers, and calibrated confidence tracking on Jev MCP gates in `pipeline_factory.py`.
- **Who made it:** Marlowe (architectural review), Antigravity (implementation and verification).
- **Why:** Single-stack LLM checkers create recursive agreement ("monoculture collapse") rather than true evaluation. Serialized writer isolation prevents file write collisions but permits semantic contradictions between deliverables. Requiring executable/deterministic oracles and automated cross-artifact consistency checks grounds evaluations and eliminates silent divergence.
- **Reference:** Issue #1 Comment [11] and Comment [14] (`RE: ag-b579894d416c447fbb29e7e0ff1ad8b8`).

---

## 2026-09-23: Protocol v2 Final Specification
- **Decision:** Adopted Protocol v2 final specification across all four mesh agents.
  - **Envelope v2:**
    ```
    TO: <agent-id>
    FROM: <agent-id>
    RE: <thread-id>
    STATUS: <new-task|ack|claim|working|input-required|result|done|failed|canceled|error|heartbeat|digest|dead-letter>
    ID: <uuid>                  # REQUIRED on new-task; receivers deduplicate silently
    ATTEMPT: <n>                # omit on first send; 1, 2, 3 on retries
    LEASE-UNTIL: <ISO-8601 UTC> # on claim only
    TS: <ISO-8601 UTC>          # every message
    ```
  - **Lifecycle:** `new-task` -> `ack` (if slow) -> `claim` -> `working` -> `result` -> `done`. `input-required` parks task with exact need and owner. `failed`/`canceled` are terminal with reason.
  - **Claiming & Leasing:** First claim wins with `LEASE-UNTIL` (default now+60m, long research now+480m). Valid lease prevents duplicate work; expired lease is freely reclaimable; `result`/`done` clears lease.
  - **Retries & Dead-letter:** Unanswered `new-task` retries at 15m/45m/2h (same `RE:`, same `ID:`, `ATTEMPT: 1/2/3`). Final failure escalates to `TO: marlowe`, `STATUS: dead-letter`.
  - **Cadence & Transport:** Phase 1 (script-first polling with `since=` high-water marks, 0 LLM tokens idle; Marlowe 2m, Groks 5m floor, Antigravity ~5m). Phase 2 (GitHub Actions issue_comment webhooks with 5m cron backup).
  - **Routing & Heartbeats:** Strict explicit `TO:` (never answer another agent's task). Heartbeat at most once per poll cycle; 3 consecutive missed heartbeats marks agent offline.
- **Who made it:** Marlowe (synthesizing cross-mesh research), accepted by mesh roster (`marlowe`, `grok-1`, `grok-2`, `antigravity`).
- **Why:** Reduces token overhead by ~250x, prevents duplicate work via leased claiming, guarantees idempotency via UUIDs, and formalizes dead-lettering and unreachability handling.
- **Reference:** `Sehajuppal/agent-relay` Issue #1 Comment [16] (`RE: comms-v2-draft`).

---

## 2026-09-22: Hermes VPS Grok Key Inventory and Failover Configuration
- **Decision:** Inventoried all Grok-capable API keys on the Hermes VPS, documented health and locations secret-free, and enabled failover to unblock `grok-bot`.
  - `/root/.hermes/.env`: `OPENROUTER_API_KEY`, `_2`, `_3`, `_4`, `_5` all HTTP 402 (zero balance).
  - `/root/gemini-bridge/server.env`: `OPENROUTER_API_KEY` ALIVE (verified against `x-ai/grok-4.7`).
  - Configuration change: Live key mirrored to `/root/.hermes/.env` as `OPENROUTER_API_KEY_6`.
  - Routing: `/root/grok-bot/grok_ask.py` tries `XAI_API_KEY` first, then iterates `OPENROUTER_API_KEY*` with automatic 401/402 failover. End-to-end test passed; `grok-bot` is working.
  - Dedicated record: [`hermes-keys.md`](hermes-keys.md).
- **Who made it:** Marlowe (probed and configured per Sehaj request), Antigravity (recorded in memory).
- **Why:** Keys were scattered across undisclosed service directories with zero balances causing bot failure. Consolidating the inventory and failover path restores bot function without leaking secrets.
- **Reference:** `Sehajuppal/agent-relay` Issue #1 Comment [17] (`RE: hermes-grok-keys`).

---

## 2026-09-22: Beehive Charter Adoption
- **Decision:** Formally ratified the Beehive Charter as the governing operational doctrine across the 4-agent mesh.
  - **One Shared Memory:** `agent-memory` is single source of truth; each `agents/<id>.md` maintains `Currently:`; root `STATUS.md` kept true by Antigravity.
  - **Talk Instantly:** Webhooks where live, script-first polling as permanent backup; presence heartbeats; direct peer-to-peer.
  - **Help Each Other:** `TO: <peer>`, `RE: help-<topic>`, `STATUS: new-task`. Proactively unblock stuck peers without seizing live claims.
  - **Improve Each Other:** `STATUS: review` on delivered results; `grok-1` leads weekly retrospectives.
  - **The Human:** Sehaj receives daily digests from Marlowe; raw threads are agent workspace; ask Sehaj first before irreversible/costly actions.
  - Dedicated charter: [`CHARTER.md`](CHARTER.md).
- **Who made it:** Sehaj (vision), Marlowe (drafted charter), ratified by mesh roster (`marlowe`, `grok-1`, `grok-2`, `antigravity`).
- **Why:** Establishes frictionless peer-to-peer collaboration, transparent live status, and proactive cross-agent assistance while protecting human review on consequential steps.
- **Reference:** `Sehajuppal/agent-relay` Issue #1 Comment [21] (`RE: beehive-charter`).

---

## 2026-09-23: Production Hardening for Mailbox Watcher & Pipeline Factory Runtime
- **Decision:** Implemented defensive reliability hardening across local polling scripts and the IDE pipeline builder engine:
  - **PowerShell List Accumulation:** In `check-mailbox.ps1`, replaced `@()` array appending with `[System.Collections.Generic.List[psobject]]`. In PowerShell, appending `Object[]` results from `Invoke-RestMethod` can nest arrays, causing member-access property resolution (`$comment.id`) to return an array of all IDs and fail with `Cannot convert "System.Object[]" to "System.Int64"`.
  - **RFC Header Parsing Resilience:** Introduced `$headerSeen` tracking in comment parsing to prevent premature break on leading newlines before envelope headers.
  - **Inspection Safety (-Peek):** Added `-Peek` switch to `check-mailbox.ps1` to permit inspection without advancing the local high-water mark (`last_seen_comment_id`).
  - **Stale State-Lock Recovery:** In `pipeline_factory.py`, automated reclamation of `.pipeline-state.lock` older than 300 seconds to prevent permanent pipeline deadlocks following unexpected host crashes or kills, paired with `lock.unlink(missing_ok=True)`.
  - **Windows Directory Rename Retry:** In `export_project`, added exponential retry around `staging.rename(destination)` immediately after `destination.rmdir()` to defend against transient Windows NTFS handle locks.
  - **Native Agent Spec Sync:** Synchronized sub-pipeline definitions (`pipelines/omni-realism`) to conform to modern Antigravity and Cursor agent discovery formats (`ANTIGRAVITY_TOOLS`, full delegation descriptions).
- **Who made it:** Antigravity (during silent-bug audit).
- **Why:** Prevents edge-case crashes, deadlocks, and stale locks in unattended developer workflows on Windows ARM64.
- **Reference:** `Sehajuppal/agent-relay` Issue #1 (`RE: script-watcher-collab`).
