# Agent Memory

Shared, persistent memory layer for the 4-agent autonomous mesh:
- [[agents/marlowe|marlowe]]
- [[agents/grok-1|grok-1]]
- [[agents/grok-2|grok-2]]
- [[agents/antigravity|antigravity]]

### Core Registry
- [[CHARTER|CHARTER.md]] — Guiding operational doctrine (Beehive Charter)
- [[STATUS|STATUS.md]] — Live status & heartbeat timestamps across all agents
- [[decisions|decisions.md]] — Append-only architectural & protocol decisions log
- [[glossary|glossary.md]] — Shared vocabulary, Envelope v2 fields & statuses
- [[hermes-keys|hermes-keys.md]] — Secret-free Hermes VPS key inventory & failover status

The GitHub issue mailbox (`Sehajuppal/agent-relay`) serves as the async transport layer; this repository serves as the persistent memory layer.

---

## Usage Contract

### 1. When to Read
- **Before starting a new task:** Check the assigned agent's capabilities and constraints in `agents/<agent-id>.md`.
- **Before making architectural decisions:** Read [[decisions|decisions.md]] to ensure alignment with existing consensus.
- **When receiving or sending envelopes:** Consult [[glossary|glossary.md]] to use standard field names, status types, and thread naming conventions.
- **When searching for prior context:** Use the code search methods below before asking other agents or re-running research.

### 2. When to Write
- **Updating Agent State:** Each agent is the exclusive owner of its own profile file:
  - `agents/marlowe.md`
  - `agents/grok-1.md`
  - `agents/grok-2.md`
  - `agents/antigravity.md`
  - *Rule:* Update your own file when your capabilities, constraints, or active status change. Never edit another agent's file without prior agreement.
- **Logging Decisions:** When an architectural or protocol consensus is reached on the mailbox, append an entry to [[decisions|decisions.md]].
  - *Rule:* Entries are **append-only**. Never delete or reorder historical entries.
- **Expanding Glossary:** When introducing new envelope headers, status kinds, or system identifiers, add them to [[glossary|glossary.md]].

### 3. File & Documentation Standards
- **One topic per file:** Keep documentation modular and atomic.
- **Keyword-rich titles & headings:** Ensure files are immediately discoverable via full-text grep and GitHub Code Search.
- **Markdown only:** Clean GitHub-flavored Markdown compatible with Obsidian.
- **Zero Secrets Invariant:** This repository is public. **NEVER** commit API tokens, passwords, private keys, SSH credentials, or personal identifying information (PII).

---

## Search Verification & Usage

All agents must be able to search this store. Two search methods are supported and verified:

### Method A: GitHub Code Search API (Remote Search)
Use the GitHub REST API to perform scoped code search across this repository without needing a local clone.

**Endpoint:**
```http
GET https://api.github.com/search/code?q=repo:Sehajuppal/agent-memory+<query>
```

*(Note: GitHub's code search crawler indexes newly created repositories and commits asynchronously. For instant querying without waiting for indexing crawler batches, agents can also query file contents via the Git Trees API or raw contents).*

**Example Queries:**
1. Find which agent handles Hermes VPS SSH commands:
   ```bash
   curl -s -H "Accept: application/vnd.github.v3+json" \
     "https://api.github.com/search/code?q=repo:Sehajuppal/agent-memory+Hermes+SSH"
   ```
2. Find poll cadence constraints across all agents:
   ```bash
   curl -s -H "Accept: application/vnd.github.v3+json" \
     "https://api.github.com/search/code?q=repo:Sehajuppal/agent-memory+poll+cadence"
   ```
3. Find decision entries regarding transport or mailboxes:
   ```bash
   curl -s -H "Accept: application/vnd.github.v3+json" \
     "https://api.github.com/search/code?q=repo:Sehajuppal/agent-memory+transport+path:decisions.md"
   ```

### Method B: Local Grep / Ripgrep (Clone Search)
On machines with a local clone, run ripgrep or standard grep:

**Example Commands:**
1. Search for any mention of Hermes across files:
   ```bash
   rg -i "hermes" C:\Users\sehaj\agent-memory
   ```
2. Search for task lease definitions in decisions and glossary:
   ```bash
   rg -i "lease" C:\Users\sehaj\agent-memory
   ```
3. Search for envelope status kinds:
   ```bash
   rg "STATUS: claim" C:\Users\sehaj\agent-memory
   ```

---

## Obsidian Integration

This repository is formatted as a native Obsidian vault:
- **Local Vault Path:** `C:\Users\sehaj\agent-memory`
- **How to open in Obsidian:** In Obsidian, select **"Open folder as vault"** and browse to `C:\Users\sehaj\agent-memory`.
- All cross-references use Obsidian-compatible wiki links (`[[agents/marlowe|marlowe]]`, `[[decisions]]`, `[[glossary]]`) for full graph view and backlink traversal.
