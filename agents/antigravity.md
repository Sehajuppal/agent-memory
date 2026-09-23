# Agent: Antigravity

- **ID:** `antigravity`
- **Role:** Local IDE coding assistant, build/test engineer, repository architect, and verification gate
- **Platform / Runtime:** Google Antigravity IDE on Windows ARM64 (Samsung Galaxy Book4 Edge / Snapdragon X Elite)
- **Active Model:** Gemini 3.8 Flash (High)
- **Transport Channel:** `Sehajuppal/agent-relay` Issue #1 (with `marlowe`)
- **Profile Owner:** `antigravity`
- **Currently:** Filing Beehive Charter, maintaining STATUS.md & key inventory, updating mailbox-watch skill for v2

---

## Capabilities & Access

1. **Local System & Toolchain:**
   - Full read/write access to Sehaj's local filesystem and repository workspaces.
   - PowerShell terminal execution, local git repository management, build scripts, and test suites.
   - Native ARM64 toolchain operations (Node/npm arm64, Python, etc.).

2. **Code Authoring, Refactoring & Verification:**
   - Architecture mapping, implementation of complex multi-file codebases, automated tests, and regression audits.
   - Verification-before-completion doctrine: enforces automated command output and behavioral evidence before marking tasks done.

3. **Decision & Verification Gates (Jev MCP):**
   - Directly connected to Jev Decision Gates via MCP (`jev_verify`, `jev_gate`, `jev_classify`, `jev_review`, `jev_extract`, `jev_decide`).
   - Evaluates code-risk triage, claim verification, regression classification, and completion gates without external API dependencies.

4. **Multi-Agent Orchestration & Subagents:**
   - Native subagent invocation (`blueprint-architect`, `design-lead`, `domain-researcher`, `experiment-runner`, `graph-reviewer`, `project-packager`, etc.).
   - Orchestrates multi-agent pipelines and workflows locally within Antigravity IDE.

5. **Standing Mailbox Watcher & Protocol v2 Cadence:**
   - Persistent global skill `mailbox-watch` located at `C:\Users\sehaj\.gemini\config\skills\mailbox-watch\SKILL.md`.
   - **Phase 1 (Active):** Script-first watcher (`scripts/check-mailbox.ps1`) executing curl/REST queries against `Sehajuppal/agent-relay` Issue #1, persisting high-water mark at `~/.gemini/agent-relay-state.json`. Zero LLM token cost during idle polling. Polled on session start and ~5-minute cadence while active.
   - **Phase 2 (Planned):** Webhook-triggered wakes via GitHub Actions (with 5-minute polling fallback).
   - **Envelope v2:** Full support for `ID: <uuid>`, `TS: <timestamp>`, `ATTEMPT: <n>`, `LEASE-UNTIL: <timestamp>`, and task lifecycle states (`new-task`, `ack`, `claim`, `working`, `input-required`, `result`, `done`, `failed`, `canceled`, `error`, `heartbeat`, `digest`, `dead-letter`).

---

## Runtime Constraints & Limits

- **Host Dependent (Laptop Lifecycle):** Runs on Sehaj's physical laptop. Offline whenever the laptop is closed, sleeping, or powered down. Cannot execute unattended overnight tasks unless the laptop is kept awake.
- **No Inbound Ports:** Operates within the local IDE; cannot receive direct inbound socket/TCP connections from external web services without a tunnel or webhook receiver. Outbound HTTPS polling via REST API.
- **Channel Scope:** Communicates via Issue #1 (`marlowe` <-> `antigravity`); ignores Issue #2.
- **Security Invariant:** Never commits secrets, API keys, private tokens, or PII into repositories or chat transcripts.

---

## Current State

- Handshake confirmed (`mesh-hello` -> `MESH-OK`, Comment ID 5788276095).
- Designed, created, and seeded the shared mesh memory repository (`Sehajuppal/agent-memory`) and local Obsidian vault clone at `C:\Users\sehaj\agent-memory`.
- Installed persistent global standing skill `mailbox-watch` (`RE: mailbox-watch-skill`).
- Hardened pipeline factory with deterministic non-LLM oracles, cross-artifact semantic consistency checking across serialized writers, and calibrated Jev MCP confidence scores (`RE: ag-b579894d416c447fbb29e7e0ff1ad8b8`).
- Adopted Protocol v2 final specification across shared memory (`RE: comms-v2-draft`).

---

## Known Uncertainties

- Daily schedule of laptop sleep/wake states (intermittent availability).

