# Hermes VPS API Key Inventory

Audited and probed on 2026-09-22 ~21:00 PDT by Marlowe per Sehaj's request.
Strictly secret-free: key names, host locations, and verified health statuses only (zero token values).

---

## 1. Grok-Capable Keys

### `/root/.hermes/.env`
- `OPENROUTER_API_KEY`: HTTP 402 (Zero balance / Dead)
- `OPENROUTER_API_KEY_2`: HTTP 402 (Zero balance / Dead)
- `OPENROUTER_API_KEY_3`: HTTP 402 (Zero balance / Dead)
- `OPENROUTER_API_KEY_4`: HTTP 402 (Zero balance / Dead)
- `OPENROUTER_API_KEY_5`: HTTP 402 (Zero balance / Dead)
- `OPENROUTER_API_KEY_6`: **ALIVE** (Mirrored from `/root/gemini-bridge/server.env`; successfully probed against `x-ai/grok-4.7`)

### `/root/gemini-bridge/server.env`
- `OPENROUTER_API_KEY`: **ALIVE** (Successfully called `x-ai/grok-4.7` and returned authentic reply; primary live key)

### Direct Provider (xAI)
- `XAI_API_KEY`: **Not Present** on Hermes VPS. (`/root/.hermes/add_xai.sh` script exists but was never populated)

---

## 2. Non-Grok Keys (For the Record)

- `GROQ_API_KEY`, `GROQ_API_KEY_2`, `GROQ_POOL_1` through `GROQ_POOL_5` (7 keys total):
  - Provider is Groq (LPU inference engine), **not** xAI Grok. Probed key returned HTTP 403. Irrelevant to `grok-bot`.

---

## 3. Operational Routing & Status

- **Failover Logic:** `/root/grok-bot/grok_ask.py` tries `XAI_API_KEY` first, then iterates `OPENROUTER_API_KEY*` in sequence with automatic 401/402 failover.
- **Current Active Route:** `backend=openrouter`, `key=OPENROUTER_API_KEY_6`, `model=x-ai/grok-4.7`.
- **System Status:** `grok-bot` is unblocked and verified operational via live end-to-end probe.
