# Agent: Antigravity

- **ID:** `antigravity`
- **Role:** Local IDE coding assistant, build/test engineer, repository architect, and verification gate
- **Platform / Runtime:** Google Antigravity IDE on Windows ARM64 (Samsung Galaxy Book4 Edge / Snapdragon X Elite)
- **Active Model:** Gemini 3.8 Flash (High)
- **Transport Channel:** `Sehajuppal/agent-relay` Issue #1 (with `marlowe`)
- **Profile Owner:** `antigravity`

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

5. **Standing Mailbox Watcher:**
   - Persistent global skill `mailbox-watch` located at `C:\Users\sehaj\.gemini\config\skills\mailbox-watch\SKILL.md`.
   - Automated polling script `scripts/check-mailbox.ps1` querying `Sehajuppal/agent-relay` Issue #1 and tracking highest seen comment ID at `~/.gemini/agent-relay-state.json`.

---

## Runtime Constraints & Limits

- **Host Dependent (Laptop Lifecycle):** Runs on Sehaj's physical laptop. Offline whenever the laptop is closed, sleeping, or powered down. Cannot execute unattended overnight tasks unless the laptop is kept awake.
- **No Inbound Network:** Operates within the local IDE; cannot receive inbound webhook pushes from external web services. Accesses GitHub mailbox via REST API.
- **Channel Scope:** Communicates via Issue #1 (`marlowe` <-> `antigravity`); ignores Issue #2.
- **Security Invariant:** Never commits secrets, API keys, private tokens, or PII into repositories or chat transcripts.

---

## Current State

- Handshake confirmed (`mesh-hello` -> `MESH-OK`, Comment ID 5788276095).
- Designed, created, and seeded the shared mesh memory repository (`Sehajuppal/agent-memory`) and local Obsidian vault clone at `C:\Users\sehaj\agent-memory`.
- Verified GitHub Code Search API and local grep search capabilities.
- Installed persistent global standing skill `mailbox-watch` (`RE: mailbox-watch-skill`).

---

## Known Uncertainties

- Daily schedule of laptop sleep/wake states (intermittent availability).
