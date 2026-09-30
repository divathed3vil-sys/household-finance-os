# AI SHARING POLICY — what may leave this machine

**Purpose:** implementation AIs (DeepSeek, ChatGPT, Claude, Copilot…) get everything they need to
work — while secrets and family data never leave. Rule of thumb: anything pasted to a cloud AI
should be assumed retained by that provider.

## 🟢 GREEN — paste freely to any AI

- `docs/ai/SESSION-PROMPT.md` · `AI-CONTEXT.md` · `CODING-RULES.md` · `00-READ-ME-FIRST.md` ·
  this policy
- `docs/ai/phases/*.md` (task specs) · `docs/ai/PROGRESS.md`
- `docs/MASTER-SPEC.md` — the design. Our security comes from keys + RLS + correct code, **not**
  from design secrecy (Kerckhoffs' principle). The demo/portfolio will present this system anyway.
- `docs/SETUP-GUIDE.md`
- Application code, `pubspec.yaml`, public package docs, fictional/demo seed data
- Command outputs and stack traces **after** the RED-scan below

## 🟡 AMBER — share only when the task needs it; minimize and redact first

- **Error logs / stack traces** — scan for: tokens (`eyJ…` JWTs), URLs with query strings, device
  serials, personal emails. Redact before pasting.
- **Screenshots** — never real balances/names/receipts. Use demo-data builds for screenshots.
- **Bug descriptions** — retell with fictional amounts/labels ("Rice Rs. 850"), never copy real rows.
- **Project URL + anon key** — the anon key is public-by-design (RLS guards the data) and ships in
  the app anyway, but share only when the AI genuinely needs the exact config.

## 🔴 RED — never paste to any AI/chat; never commit to Git

- `service_role` key (the master key — total database access)
- Database password, storage tokens, any CI secret *values*
- `.env`, `--dart-define` *values*, keystore files and passwords
- **Real** family data: transactions, balances, budgets, vault contents, audit exports, backups,
  receipt/vault photos
- Session tokens, cookies, QR/TOTP secrets, recovery codes
- Family PII beyond the first-name personas already in the docs

> Once the REAL Supabase project exists (Phase 4/go-live), everything about it is at least AMBER,
> and its data is RED by default. The demo project is the only one whose data may be shared
> (because it's fictional).

## Session recipe — how to actually run an AI session

**Repo-aware agents** (Copilot Workspace / Cursor-style, anything with file access):
they read the repo themselves. Paste only the **short variant** of `SESSION-PROMPT.md` with the
phase file + task ID. Add nothing else.

**Chat agents** (ChatGPT/Claude/DeepSeek in a browser):
1. Paste `SESSION-PROMPT.md` **filled in** with `<PHASE_FILE>` and `<TASK_ID>`.
2. Then paste file contents **in read order**, only what's listed:
   `AI-CONTEXT.md` → `CODING-RULES.md` → `PROGRESS.md` → the current phase file → **only the
   MASTER-SPEC sections the phase file lists under "Spec refs"** (each phase file carries this
   list — keeps context tight and the drift surface small).
3. At the end of the session, take the AI's **Verification Report** + its proposed `PROGRESS.md`
   lines, review, commit (`git commit`) — the human commits, always.

**RED-scan (10 seconds, before pasting any output/log):** search for `eyJ` (JWTs), `supabase.co`
URLs with `?`, `service_role`, `password`, phone numbers, real amounts you recognize. If found →
replace with `<redacted>`.
