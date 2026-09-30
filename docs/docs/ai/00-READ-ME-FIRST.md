# BUILD CONTROL SYSTEM — how this project is built with AI assistance

This project is implemented by AI coding agents (Claude, GPT, Copilot — any model), directed by
the human owner. AI models hallucinate and drift **unless the project's memory lives in files
instead of chat**. This folder is that memory. Everything an AI needs to know is here; every
session follows the same ritual. Switching models mid-project loses nothing.

## The files

| File | Role |
|---|---|
| `../MASTER-SPEC.md` | **THE LAW.** What we are building and why. AI agents never edit it. |
| `AI-CONTEXT.md` | Locked decisions + non-negotiable invariants. Read every session. |
| `CODING-RULES.md` | Engineering conventions and the forbidden list. Read every session. |
| `PROGRESS.md` | Living tracker: current phase, task states, verification log. |
| `SESSION-PROMPT.md` | The template pasted into any AI at session start. |
| `phases/phase-XX-*.md` | One phase = goal + tasks + acceptance criteria + verification commands. |

## The session ritual (every AI, every session)

1. **Read** (in order): `AI-CONTEXT.md` → `CODING-RULES.md` → `PROGRESS.md` → current phase file →
   the referenced MASTER-SPEC sections.
2. **State** understanding: current phase, the ONE task it will do, plan, files it expects to touch.
3. Wait for the human's **"go"**.
4. **Implement** only that task. Nothing else.
5. **Verify**: run the acceptance checks from the phase file (build, analyze, tests, manual steps).
6. **Report**: fill the Verification Report template (in `SESSION-PROMPT.md`), including any
   deviation — no silent deviations.
7. **Update** `PROGRESS.md`: tick the task, add a verification-log row.
8. **Stop.** The human reviews, commits, and starts the next session.

## Rules for the human owner

- **One task per session.** Small loops beat big ones; drift compounds in long sessions.
- **Commit after every verified task** (`git commit`). If an AI wrecks something: `git checkout .` /
  `git reset --hard HEAD` restores the last verified state instantly.
- **Never let an AI edit `MASTER-SPEC.md` or `AI-CONTEXT.md`.** Spec changes happen by discussion
  with the architect, then the architect updates the spec, then phase files follow.
- If an AI claims a task is done but can't show acceptance-check output → it is not done.
- If an AI is confused or the spec is ambiguous → the correct answer is "STOP and ask", never
  "invent something plausible".

## Sharing with external AIs
Follow `AI-SHARING-POLICY.md` (GREEN/AMBER/RED). Golden rule: specs and code travel freely;
keys, secrets, and real family data never do.

## Architect anti-drift protocol (binds the planner/architect role too)

1. **Position statement first.** Every work turn opens with: current position from `PROGRESS.md`,
   what this turn changes, which spec sections it touches.
2. **Locked decisions are numbered** (see `AI-CONTEXT.md`). A proposal contradicting one must
   explicitly declare **"AMENDS DECISION №N — reason"** and update `AI-CONTEXT.md` the same turn.
   Silent contradiction = drift.
3. **The Amendment Log** (`MASTER-SPEC.md` appendix) is the ONLY lawful way the plan changes after
   Phase-1 approval: numbered entries (A-01, A-02…) with date, rationale, affected sections.
   Chat-only "let's do X instead" has no force until logged.
4. **Tracker sync at end of turn.** Any status change lands in `PROGRESS.md` in the same turn.
5. **Owner stop-phrase:** "STOP — check the spec" → the architect must cite the controlling
   section, or concede and log an amendment.
6. **Phase files carry "Spec refs".** Every phase file lists the exact MASTER-SPEC sections it
   implements; implementation agents paste only those (see `AI-SHARING-POLICY.md` recipe).

## If things go wrong

- AI produced garbage → `git reset --hard HEAD` → re-paste `SESSION-PROMPT.md` fresh → redo task.
- AI contradicts the spec → spec wins; paste the relevant spec section and make it comply.
- Totally lost → open `PROGRESS.md`, see the last verified checkpoint, resume from there.
