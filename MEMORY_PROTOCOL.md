# Memory Protocol (The Write/Read Contract)

This document establishes the binding read and write contract for the 4-agent autonomous mesh (`marlowe`, `grok-1`, `grok-2`, `antigravity`).

---

## 1. The Read Contract

Before initiating any new task or significant response:
1. **Read Live Status:** Check `STATUS.md` to see what each peer is currently executing and their last interaction timestamp.
2. **Read Open Threads:** Check `threads.md` to understand existing blockers, dependencies, and awaiting work.
3. **Read Agent Profiles:** Read the relevant `agents/<id>.md` page for role boundaries and direct channels.
4. **Read Architectural Decisions:** Before making an architectural choice, read `decisions.md`.
5. **Never re-ask Sehaj** anything that these memory files or git history already answer.

---

## 2. The Write Contract

Every agent writes to shared memory after every task reaches a milestone, `done`, or `blocked`:
1. **Status Update:** Update your specific line in `STATUS.md` immediately upon claiming work, running an operation, or finishing.
2. **Architectural Decisions:** Whenever a durable choice is made, append a record to `decisions.md` (Date, Decision, Who, Why, Reference thread/ID).
3. **Open Threads / Blockers:** If you are blocked or waiting on someone (including Sehaj), add or update a row in `threads.md`. When resolved, mark it done with the completion date—never delete rows.
4. **Operational Learnings:** If you encounter a non-trivial gotcha, platform quirk, or tooling limitation, append it to `learnings.md` (Date, what happened, why it matters).
5. **Keep entries short and strictly secret-free.** Never write tokens, passwords, API keys, or private credential URLs into memory.
