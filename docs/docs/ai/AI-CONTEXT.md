# AI CONTEXT — read at the start of every session

> Single source of truth = `docs/MASTER-SPEC.md`. This file is the short, stable summary.
> If anything here conflicts with the spec, the spec wins — report the conflict, don't improvise.

## What this is

**Household Finance OS** — a private, cash-first finance app for one Sri Lankan family (4 members),
Android + web, one codebase, one backend. ALSO a portfolio piece: a public DEMO deployment with
fictional data demonstrates every feature. Real family data and demo data NEVER share a database.

## Users & roles

| Person | Role | Core need |
|---|---|---|
| Appa | Household Admin | Plan/allocate/monitor everything; owns the Vault |
| Amma | Household Expense User | Near-zero-friction expense + cash entry |
| Diva | Normal User **and** Super Admin | Personal tracking; maintenance powers via a separate audited role-context (step-up to activate) |
| Anu | Normal User | Daily part-time income + expenses |

"Silent" super-admin actions = **no Appa notification, but ALWAYS an audit record**. Never silent to the log.

## Locked decision record (do not relitigate)

1. Flutter (Android + web) + Supabase (Postgres, Auth, RLS, Storage, Realtime, Edge Functions). Modular monolith.
2. Supabase managed cloud, **Singapore region**. Demo project: `hfos-demo`.
3. Offline-first **capture**: local outbox with client-generated idempotency keys for expense/income/cash/transfer entries; sync on connectivity. No double-posting. Dashboards may assume connectivity in V1.
4. Each member: own Android phone, individual account, per-device sessions.
5. Sign-in: **username + password** (no email UX; Supabase Auth with internal emails). Biometric app unlock + password fallback. Diva (Super Admin) resets credentials.
6. Alerts: in-app center + private **Telegram bot** (Edge Functions + cron).
7. Receipt photos in **MVP**: attach in entry flow, queue as pending uploads in the outbox.
8. Budgets: calendar month, **per-section rollover toggle** applied at next-month generation.
9. Demo: separate Supabase project + URL, 5 one-tap demo personas (`demo1234`), nightly reseed via cron.
10. Money: **integer cents**, LKR-only V1 (currency column for future), append-only ledger with source→destination accounts, **computed** balances (never stored as truth), void-not-delete.
11. Distribution/testing: GitHub repo + Actions CI → debug APK attached to **GitHub Releases** (phone installs from the Releases page; artifacts are CI-only since they expire/require login) and `flutter build web` deployed to **GitHub Pages** (live URL; doubles as Appa's PC access and later the public portfolio demo, pointed only at `hfos-demo`). Dev machine note: Android SDK via command-line tools only (no Android Studio) — accepted; JDK 17 present.
12. **No USB-debugging dependency (Amendment A-01).** Family phones must keep Developer Options OFF (banking apps self-disable otherwise). Dev iteration = `flutter run -d chrome` (hot reload); optional emulator if wanted. Phone testing + family distribution = APK installed via GitHub Releases or USB file-copy (MTP; one-time "install unknown apps") — exactly how the family will receive the app in production.
13. **Repo `household-finance-os` is PUBLIC during development** (portfolio artifact; free GitHub Pages; login-free APK downloads for phones). It references ONLY the demo project `hfos-demo`; secrets never committed; security = keys + RLS, not blueprint secrecy. Real-deployment hosting/privacy model at go-live = **open question OQ-1** (decided Phase 09, not before).

## Non-negotiable invariants (violating these = bug, always)

- `remaining = allocated − spent` is **computed from transactions**, never stored.
- A transaction moves value **from** an account **to** an account; it cannot create/destroy money.
  Income/expense use virtual external accounts. Transfers are never income or expense.
- Split items MUST sum exactly to the parent transaction amount.
- Every sensitive mutation writes an **append-only audit event** (actor, role-context, old, new, ts, reason, session).
- **RLS on every table.** Client-side hiding is UX, never authorization.
- Corrections = reversal/adjustment entries. Hard-deleting financial rows is forbidden.
- Every entry operation carries an **idempotency key**; syncing never duplicates.
- The service_role key never appears in client code, Git, or logs.

## Glossary

- **Wallet** — a person's physical-cash account. Distinct from a budget's "remaining".
- **Ledger** — the append-only set of transactions that is the sole source of truth.
- **Outbox** — local queue of unsynced entries (expoenses/income/transfers/receipts) with idempotency keys.
- **Vault** — Appa's protected area (FDs, insurance, trackers, documents); step-up auth.
- **Tracker** — flexible/custom-field financial instrument (FD, insurance, loan, …).
- **Role context** — which role a session is operating as (Diva: normal vs super-admin).
