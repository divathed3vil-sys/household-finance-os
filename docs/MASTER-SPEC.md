# HOUSEHOLD FINANCE OS — MASTER TECHNICAL SPECIFICATION

**Status:** DRAFT — underway. Parts 0–2 written, PENDING OWNER REVIEW. Phases 3–8 follow.
**Authority:** This document is the single source of truth ("the law") for all implementation.
Only the architect (with the owner) edits it. Implementation agents read; they never write.
**History:** Planning commissioned 2026-09-28. Decisions №1–11 locked in `docs/ai/AI-CONTEXT.md`.

**Conventions used throughout**
- Requirement IDs: `FR-<domain>-<n>` (functional), `NFR-<domain>-<n>` (non-functional),
  `INV-<n>` (financial invariants), `SCR-<n>` (screens), `RISK-<type>-<n>`.
- Normative language: **MUST** = required for correctness/security · **SHOULD** = expected unless
  justified · **MAY** = optional.
- Money: written `Rs. 12,500` in prose; stored as integer cents everywhere (§10).
- "V1" = the MVP + accepted scope decisions. "v1.1" and "Future" per §84–86 (Phase 8) and the
  scope classifications inline (`[MVP]` / `[v1.1]` / `[FUTURE]`).

---
---

# PHASE 1 — PRODUCT SPECIFICATION

## §1 Product vision

A private **household financial operating system** for one Sri Lankan family of four. Cash-first,
plan-vs-actual at its core: **Appa defines the plan, people record what actually happened, the
system continuously calculates the difference.** It is not a bank client, not a spreadsheet, not
accounting software — it is a calm, fast daily tool whose data model is rigorous enough that
*every rupee can be explained*.

Dual purpose: the same codebase powers (a) the private family deployment and (b) a public,
fictional-data **demo deployment** that doubles as a portfolio exhibit of engineering quality.

## §2 Goals

| # | Goal |
|---|---|
| G1 | Amma records an expense in ~3 interactions / ≤10 seconds, offline-safe — every time, no friction. |
| G2 | Appa sees household financial state at a glance (cash, budgets, spend, commitments) without doing data entry. |
| G3 | Diva/Anu log personal income+expenses daily with near-zero effort; personal analytics follow automatically. |
| G4 | One authoritative ledger: balances, budget remaining, analytics are all *computed*, never hand-maintained. |
| G5 | Physical cash is a first-class citizen alongside bank balances. |
| G6 | Security is structural (RLS + audit + role contexts), generosity of UX never bypasses it. |
| G7 | v1.1: replace Appa's FD/insurance spreadsheet with flexible trackers + countdowns + reminders. |
| G8 | Portfolio-grade demo with zero real data, hard-isolated from the family deployment. |

## §3 Non-goals

- **N1** No direct bank integration / no bank credentials stored (V1).
- **N2** Not double-entry bookkeeping UX — accounting rigor stays under the hood; users see plain language.
- **N3** No LLM/AI features in V1 (suggestions are deterministic; architecture leaves room later).
- **N4** Not multi-household SaaS — exactly one household (+ the fictional demo household).
- **N5** No investment trading, no crypto, no bill payment execution.
- **N6** No native iOS (Android + web only).
- **N7** No social/sharing features.
- **N8** No enterprise infrastructure (microservices/K8s/event buses) — modular monolith by design.

## §4 User personas

- **APPA (50s), Household Admin.** Salaried; manages FDs, insurance, the family plan. Time-poor,
  detail-oriented. Smartphone + company PC (browser). Will *not* do routine data entry; wants
  answers (where did it go? are we on plan? what's coming due?). Treats the app as his control
  room. Owns the Vault.
- **AMMA (40s–50s), Household Expense User.** Runs daily household spending, heavily cash-based.
  Smartphone-only. Low tolerance for forms, logins, or anything that "asks questions". Success =
  the app never slows her down and never loses an entry. Trust is earned by instant, visible saves.
- **DIVA (20s), Normal User + Super Admin.** Part-time teaching income (irregular, daily logging).
  Also the system's developer/operator. Two cleanly-separated role contexts (§5 R5a/R5b).
  Portfolio stakeholder: the demo deployment showcases her engineering.
- **ANU (late teens/20s), Normal User.** Part-time income, personal spending, savings goals.
  Smartphone-only. Needs daily income entry that tolerates skipped days gracefully.

## §5 User roles

| Code | Role | Holder | Summary |
|---|---|---|---|
| R1 | HOUSEHOLD_ADMIN | Appa | Plans, budgets, allocates, monitors, manages categories/trackers/config-grants; corrects member records (audited); owns Vault; receives security alerts. |
| R2 | HOUSEHOLD_MEMBER+ | Amma | Everything R3 can do, plus spends against household budget sections and manages shared cash flows. |
| R3 | MEMBER | Diva, Anu | Personal income/expense/cash/savings tracking, personal analytics; household visibility only per grants. |
| R4 | (same R1) | — | R1 grants to members are granular: Appa MAY grant view/edit on specific trackers/sections to anyone (§17). |
| R5a | MEMBER (normal context) | Diva | Ordinary R3 session. Violations in this context raise security alerts to Appa. |
| R5b | SUPER_ADMIN (context) | Diva | Maintenance powers: users/roles, repair any data, system config, full audit view. Activated by explicit step-up auth. Always audited; **never notifies Appa**. |

**Role context rule.** Diva has one account. Each session carries an explicit
`role_context ∈ {member, super_admin}` (§30). Switching to `super_admin` requires step-up
authentication and is itself an audited event. The distinction — not the person — determines both
permissions and alert behavior (§24).

## §6 Permission matrix (capability overview)

✓ = allowed · ◐ = per-grant/partial scope · ✗ = denied (RLS-enforced)

| Capability (detailed spec in Phase 3/4) | Appa R1 | Amma R2 | Diva R5a | Anu R3 | Diva R5b |
|---|---|---|---|---|---|
| Record own expense/income/transfer | ✓ | ✓ | ✓ | ✓ | ✓ |
| Operate own cash wallet | ✓ | ✓ | ✓ | ✓ | ✓ |
| Spend against household budget sections | ✓ | ✓ | ◐ | ◐ | ✓ |
| View own timeline + personal analytics | ✓ | ✓ | ✓ | ✓ | ✓ |
| View household dashboards/aggregates | ✓ | ◐ (relevant budgets) | ◐ | ◐ | ✓ |
| Create/edit budgets + templates, allocations | ✓ | ✗ | ✗ | ✗ | ✓ |
| Manage categories | ✓ | suggest-only | ✗ | ✗ | ✓ |
| Correct/void others' records (audited reversal) | ✓ | own recent entries | ✗ | ✗ | ✓ |
| Vault: view trackers | ✓ | ◐ per grant | ◐ per grant | ◐ per grant | ✓ |
| Vault: create/edit trackers & documents | ✓ | ✗ | ✗ | ✗ | ✓ |
| Manage per-tracker view/edit grants | ✓ | ✗ | ✗ | ✗ | ✓ |
| Manage users, roles, sessions (revoke) | ✗ | ✗ | ✗ | ✗ | ✓ |
| View audit log | ◐ household scope | ✗ | own events | own events | ✓ full |
| Receive security-alert notifications | ✓ | ✗ | ✗ | ✗ | ✗ (generates, never receives) |
| System config, backups, demo reseed | ✗ | ✗ | ✗ | ✗ | ✓ |

## §7 Core user journeys (detailed flows in Phase 5)

1. **AMMA / fast expense:** open → (biometric unlock) → cash visible → + Expense → amount → label
   w/ autocomplete → (optional split/receipt) → save → timeline + budget + wallet update instantly.
2. **ANU / daily income:** open → + Income → amount + source → save → month income total updates.
   Skipped days need no action.
3. **DIVA / earn → save:** record income (lands in her cash wallet) → later transfer wallet→bank
   savings (separate transaction; never double-counted) → personal analytics reflect both.
4. **APPA / monthly plan:** login → dashboard review → create September from "Normal Month"
   template → adjust allocations → monitor vs actual through the month → act on alerts.
5. **DIVA (R5b) / maintenance:** step-up into Super Admin → correct a mis-entered expense → audit
   row written → no Appa notification. Contrast: R5a attempt to open the Vault → denied + audited
   + Appa notified (§24).

## §8 Functional requirements (V1 unless tagged)

**Auth & sessions**
- `FR-AUTH-01` Username + password sign-in (no email UX); per-member accounts. [MVP]
- `FR-AUTH-02` Persistent device session; explicit **Lock now**; inactivity auto-lock. [MVP]
- `FR-AUTH-03` Biometric app unlock with password fallback. [MVP]
- `FR-AUTH-04` Role-context switch (member ⇄ super_admin) requires step-up auth; audited. [MVP — context model even if switch UI lands in v1.1]
- `FR-AUTH-05` Vault entry requires step-up re-authentication. [v1.1]
- `FR-AUTH-06` Session/device list + revoke (own: self; R5b: anyone). [v1.1]

**Entry & capture**
- `FR-EXP-01` Add expense: amount + label (+category optional) saved in ≤3 interactions. [MVP]
- `FR-EXP-02` One expense MAY split across multiple categories; splits MUST sum to total (INV-03). [MVP]
- `FR-EXP-03` Receipt photo (camera/gallery) attachable at entry; queued offline upload. [MVP]
- `FR-EXP-04` Edit/void own recent entries; corrections render as reversal entries, never silent edits. [MVP]
- `FR-INC-01` Add income: date + amount + source; daily granularity; zero-days require no entry. [MVP]
- `FR-TRF-01` Cash transfer wallet→wallet between members (e.g., Appa→Amma allocation). [MVP]
- `FR-TRF-02` Cash→bank deposit; bank→cash withdrawal; cash→savings transfers. [MVP]
- `FR-ADJ-01` Balance correction against an adjustment account; mandatory reason; audited. [MVP]
- `FR-SUG-01` Label autocomplete ranked by frequency + recency + same-user history + category association; deterministic. [MVP]
- `FR-SUG-02` Accepting a suggestion applies its label + default category mapping. [MVP]

**Offline**
- `FR-OFF-01` All entry types (expense/income/transfer/receipt) write to a durable local outbox
  first and render instantly. [MVP]
- `FR-OFF-02` Sync is idempotent (client UUID key), retried with backoff; states
  `local → pending → synced | failed` are visible. [MVP]
- `FR-OFF-03` Conflicts: entries are append-only (no conflict possible); balance corrections and
  config use last-writer-wins with audit trail. [MVP]

**Budgets**
- `FR-BUD-01` Monthly budget created from (a) template, (b) previous month, or (c) blank. [MVP]
- `FR-BUD-02` Budget sections bind to a category subtree with an allocated amount. [MVP]
- `FR-BUD-03` Live per-section + total: allocated / spent / remaining / %used — always computed. [MVP]
- `FR-BUD-04` Per-section rollover toggle; applied when generating the next month (§14). [MVP]
- `FR-BUD-05` Budget-vs-actual view with member attribution. [MVP]

**Timeline, dashboards, analytics**
- `FR-TL-01` Chronological household timeline, day-grouped, member-attributed, permission-filtered. [MVP]
- `FR-DASH-01` Appa dashboard: total cash, month spend vs budget, remaining, savings, category
  breakdown, member activity, alerts. [MVP]
- `FR-DASH-02` Personal analytics per member (income, expenses, savings transfers, categories, trends). [MVP]
- `FR-ANA-01` Multi-month trends + month-over-month comparison. [v1.1]

**Trackers & commitments (v1.1)**
- `FR-TRK-01` Flexible trackers w/ custom typed fields (text/number/currency/%/date/datetime/bool/dropdown/document/note). [v1.1]
- `FR-TRK-02` Tracker timeline view + live maturity/renewal countdown (adaptive precision). [v1.1]
- `FR-RCM-01` Recurring commitments: frequency, next occurrence, payment history, ✓Paid. [v1.1]
- `FR-RMD-01` Reminders for commitments/maturities w/ configurable offsets. [v1.1]

**Audit, security, notifications**
- `FR-AUD-01` Append-only audit for money/config/permission mutations: actor, role-context, old→new, ts, session, reason. [MVP]
- `FR-AUD-02` Audit viewer: Appa = household scope; members = own events; R5b = full. [MVP]
- `FR-SEC-01` Access violation in protected area (member context) → audit + in-app alert to Appa. [MVP]
- `FR-NOT-01` In-app notification center. [MVP]
- `FR-TG-01` Telegram bot push for alerts + reminders. [v1.1]

**Categories**
- `FR-CAT-01` Seeded hierarchical English category tree (§8 of brief; full seed list in Phase 3). [MVP]
- `FR-CAT-02` Appa/R5b manage the tree (add/rename/deactivate; no destructive delete of used nodes). [MVP]

**Demo**
- `FR-DEMO-01` Five one-tap demo personas on demo URL sign-in (password `demo1234`). [MVP]
- `FR-DEMO-02` Fictional multi-month realistic dataset covering every feature. [MVP]
- `FR-DEMO-03` Nightly automated reseed (cron). [MVP — or v1.1 if cron lands late; manual reseed acceptable at launch]
- `FR-DEMO-04` Demo = separate Supabase project + URL; no code path couples it to real data. [MVP]

## §9 Non-functional requirements

- `NFR-PRF-01` Cold start ≤2.5 s on mid-range Android; entry save acknowledged ≤300 ms (local write).
- `NFR-PRF-02` Timeline/dashboard scroll at ~60 fps; virtualized lists; month views paginate.
- `NFR-OFF-01` Zero loss of offline entries across app restarts/network failures.
- `NFR-SEC-01` RLS enabled and tested on **every** table; denies verified by automated tests.
- `NFR-SEC-02` TLS in transit; provider-managed encryption at rest; encrypted backups (§37).
- `NFR-SEC-03` No third-party analytics/tracking/crash SDKs (privacy posture for family data).
- `NFR-SEC-04` service_role key never in client/git; anon key is public-by-design (RLS guards).
- `NFR-PRI-01` No bank credentials anywhere in the system (N1).
- `NFR-CMP-01` Android 8+ (API 26+) phones; web on evergreen Chrome/Edge (responsive to 360 px).
- `NFR-AVL-01` Managed cloud backups; MVP targets RPO ≤24 h, RTO ≤1 day (§38 DR plan).
- `NFR-USB-01` Amma core task (add expense) median ≤10 s, no required fields beyond amount+label.
- `NFR-LOC-01` All UI strings externalized; Tamil/Sinhala translation must not require code change.
- `NFR-PRT-01` One codebase ships both targets: Android APK (GitHub Releases) + Web (GitHub Pages).
- `NFR-MNT-01` Every screen ships loading/empty/error/offline states (Phase 5 inventory, §41).

---
---

# PHASE 2 — THE FINANCIAL SYSTEM

## §10 Financial concepts

The domain model in one paragraph: a **Household** contains **Members**. Each member owns
**Accounts** — most importantly a **Cash Wallet** (physical money on their person) and optionally
**Bank Accounts**. Value moves only via **Transactions** recorded in an **append-only Ledger**;
every transaction names a **source account** and a **destination account**. Money the family
receives comes *from* a virtual `EXTERNAL` account; money spent goes *to* it. **Categories** form
a tree; expenses (or their **splits**) point at category leaves. A **Budget** is a monthly plan
whose **sections** bind allocations to category subtrees. **Trackers** (v1.1) and **Recurring
Commitments** (v1.1) model instruments and obligations. Every sensitive mutation emits an
**Audit Event**.

**Money representation.** All amounts are `BIGINT` minor units (cents). `Rs. 12,500.50` =
`1250050`. Floats anywhere near money are defects (`CODING-RULES`). LKR-only V1; a `currency
CHAR(3) DEFAULT 'LKR'` column keeps the door open without multi-currency logic now.

## §11 Transaction model (the heart)

One table, five types. Invariant INV-02: **source and destination are always present and never
equal** — therefore no transaction can mint or vaporize money.

| Type | Meaning | Source → Destination | Consumes budget? | Analytics bucket |
|---|---|---|---|---|
| `income` | Money enters the household | `EXTERNAL` → member wallet/bank | no | Income |
| `expense` | Money leaves for goods/services | member wallet/bank → `EXTERNAL` | **yes** (its category) | Expense |
| `transfer` | Move between own-system accounts | wallet/bank → wallet/bank | no | **Excluded** from income/expense |
| `allocation` | Semantic sub-type of transfer: member→member handover with intent (e.g., Appa funds Amma) | wallet → wallet | no | Excluded (shown as "Allocated") |
| `adjustment` | Correction to reconcile reality | `EXTERNAL_ADJ` → account, or account → `EXTERNAL_ADJ` | no | Excluded; always audited w/ reason |

**Split expenses.** An expense has 1..n `transaction_splits`, each `{category_id, amount_cents,
note?}`. INV-03: `SUM(splits) == transaction.amount_cents` (DB-enforced, §25). Single-category
expense = one split — so the fast path and the split path are the same code path, never two.

**Transfer neutrality** (INV-06): analytics MUST compute income/expense from types `income` /
`expense` only. Depositing Rs. 5,000 to the bank changes account composition, never "income".

**Timeline ordering fields:** `occurred_at` (user-facing time, editable for backdating) vs
`created_at` (system). Sorting/period bucketing uses `occurred_at`; audit uses `created_at`.
All timestamps stored `timestamptz` UTC; rendered in `Asia/Colombo`.

## §12 Cash model

- Each member has exactly one implicit **Cash Wallet** (auto-created, cannot be deleted).
- `wallet_balance = SUM(inflows) − SUM(outflows)` per wallet — computed (INV-04); a cached
  materialization MAY exist for performance if provably refreshable (§27 strategy).
- **Wallets MUST NOT go negative** (INV-10). The service layer rejects overdraws; when reality
  disagrees (miscounted cash), the user records an explicit `adjustment` with a reason.
- Cash handover between members = `transfer`/`allocation` wallet→wallet; balances move atomically
  (one transaction row, two deltas).
- UX law: **"Cash available" (wallet) and "Budget remaining" (plan) are different numbers, always
  labeled as such.** A cash top-up changes the wallet; only spending changes budget remaining.

## §13 Bank account model

- `bank_accounts`: owner member, bank name, type (`savings | current | other`), notes,
  attachments (v1.1), visibility grants (Phase 4). [MVP: savings/current, owner=member or household]
- **Two balance modes** (column `balance_mode`):
  - `derived` — balance computed from transactions touching the account (disciplined users);
  - `manual` — user occasionally sets the balance; the system inserts an `adjustment` transaction
    to reconcile (audited). This honors "don't force recording every bank movement".
- Bank deposits/withdrawals are `transfer` between wallet and bank account — both sides real.

## §14 Budget model

- `budget_periods`: month (calendar, per locked decision #8) defined by `start_date/end_date`.
- `budget_templates` + `budget_template_items`: reusable plans ("Normal Month").
- `budgets` + `budget_items`: the live month. Each item = `{category_id (subtree root),
  allocated_cents, rollover:bool}`.
- **Section↔category binding (INV-09):** spending consumes a section when an expense split's
  category is inside the item's subtree. Cash handovers and transfers consume nothing.
- Computed per item and in total:
  `spent = Σ expense splits under subtree within period · remaining = allocated − spent ·
   pct_used = spent/allocated`. Remaining is **never stored** (INV-04).
- **Generation:** new month from template or copy-previous. For each source item with
  `rollover=true`, `allocated_new = allocated_base + remaining_previous` (remaining may be
  negative → budget shrinks; this is intentional feedback, not an error).
- **Editing:** allocations may change mid-month (audited). Sections may be added/removed; removing
  one with spending keeps historical reporting (item is closed, not erased).
- Overspend is *displayed* (negative remaining, amber→red), never blocked — real life overspends;
  the system informs, Appa decides.

## §15 Income model

Income = transaction type `income` into wallet or bank, with `source_label` (free text, e.g.
"Teaching") and optional income-category. Daily, irregular, zero-day-tolerant. Monthly income
totals/trends are pure `SUM`/`GROUP BY` over the ledger — no separate "income log" to reconcile.

## §16 Expense model

Expense = wallet/bank → `EXTERNAL`, 1..n splits into category leaves, fields: `label` (what was
bought — the autocomplete key), `merchant?`, `note?`, `occurred_at`, receipt attachments.
`label + category` pairs feed the suggestion engine (FR-SUG-01): ranking inputs are
global frequency, **same-user** frequency, recency (decay), and prefix/substring match score —
all computable in SQL/indexed local queries; no network needed for suggestions [offline!].

## §17 Transfer model

Four idiomatic flows, one mechanism:
1. **Handover** — Appa wallet → Amma wallet (`allocation` if earmarked, else `transfer`).
2. **Deposit** — wallet → own bank account.
3. **Withdrawal** — bank account → wallet.
4. **Saving** — wallet/bank → savings-typed bank account.
Permissions: a member may move money out of **their own** accounts; Appa R1 may additionally move
from the household funding account (his) to members. Every movement is one ledger row — atomic.

## §18 Savings model

Deliberately **not** a special subsystem: savings = a bank account with `type='savings'`.
"Money transferred to savings" is a transfer (INV-06 keeps it out of expense analytics).
Savings *progress* = that account's balance (derived) + optional target/goal field [v1.1].
This avoids the classic double-count every naive tracker makes.

## §19 Financial tracker model [v1.1, architecture fixed now]

Replace-the-spreadsheet system, schema-stable:
- `tracker_types`: kind enum seed (`fixed_deposit, insurance, investment, loan, recurring_payment,
  savings_instrument, custom`) — controls iconography/defaults only, **not** field structure.
- `trackers`: owner, name, type, status, visibility (Phase 4 grants).
- `tracker_field_defs` (per tracker): ordered, typed fields per FR-TRK-01 → EAV
  (`tracker_field_values`) with typed columns (`value_text/num/cents/date/bool/json`).
- `tracker_events`: dated timeline entries (opened, interest_updated, renewed, matured, note,
  document) → powers the chat-style timeline + countdowns. Countdowns render with adaptive
  precision (years+months when far, days+hours when near; §19 of brief, UX in Phase 5).
- FD-style computed helpers (maturity date from start+term, simple projection from principal×rate)
  are display conveniences, never write "expected interest" into the ledger as money.

## §20 Recurring commitment model [v1.1]

`recurring_commitments`: `{payee/label, amount_cents, frequency(weekly|monthly|quarterly|annual),
start, next_due, reminder_offsets:int[] (days), status}`. `commitment_occurrences`: one row per
cycle `{due_date, status(pending|paid|skipped), paid_txn_id?}`. Marking ✓Paid optionally creates
the matching expense transaction (linked) — one action, two consistent artifacts, no double entry
in analytics (the link, not a copy, is shown). A scheduler (Supabase cron + Edge Function)
advances `next_due` and emits reminders (§75).

## §21 Audit model

`audit_events` — append-only, trigger/service-written for: money mutations beyond own basic entry,
corrections/voids, adjustments, budget changes, category changes, grants/role changes, role-context
switches, vault access/mutations, security violations, session revocations, backup/export.
Fields: `actor_id, role_context, action, resource_type, resource_id, old_jsonb, new_jsonb,
reason?, session_id, device_summary, created_at`. **No UPDATE/DELETE privileges for any role**
(including R5b) — enforced by revoking grants + a `BEFORE UPDATE OR DELETE` trigger that raises.
Retention: forever (household volumes are tiny; §27). FR-AUD-02 governs who *reads* what.

**The five load-bearing invariants** (full formal list with enforcement points lands in §28/Phase 3):
- INV-01 integer cents · INV-02 source↔destination integrity · INV-03 splits sum to parent ·
  INV-04 derived balances/remaining only · INV-05 void-not-delete (history is stable) ·
  INV-06 transfer neutrality · INV-07 idempotent entries · INV-08 auditable mutations ·
  INV-09 budgets consume on expense only · INV-10 wallets never negative.

---
*Next sections to be appended:* Phase 3 — Data Architecture (ER, schema, RLS-ready tables, §22–28) →
Phase 4 — Security (§29–40) → Phase 5 — UX (§41–54) → Phase 6 — UI system (§55–67) →
Phase 7 — Technical architecture (§68–77) → Phase 8 — Build plan (§78–86) →
Appendix: Assumptions, Open Questions, Risks, MVP/Post-MVP/Future.

---
# APPENDIX — CHANGE CONTROL

## Amendment log
*The only lawful way this plan changes after Phase-1 approval. See `docs/ai/00-READ-ME-FIRST.md`
→ "Architect anti-drift protocol".*

| ID | Date | Change | Reason (why, not just what) | Sections affected |
|---|---|---|---|---|
| A-01 | 2026-09-28 | **Phone testing/distribution: removed USB-debugging dependency.** Dev iteration = `flutter run -d chrome`; phones (owner's, and later the family's) install APKs via GitHub Releases or USB file-copy (MTP) with one-time "install unknown apps". Optional emulator if desired. | The family's banking apps self-disable while Developer Options/USB debugging is active on a phone. USB debugging was never going to be the production distribution path anyway — Releases/MTP install matches reality and improves the family's security posture. | NFR-CMP-01 (unchanged, still API 26+); NFR-PRT-01 (unchanged targets); SETUP-GUIDE Stage 7–8 practice; Phase-8 §82/§86 test & deploy plans (when written); `PROGRESS.md` t00.8 acceptance |

## Open questions (resolved in order by amendment; full register lands with Phase 8)

| ID | Date raised | Question | Decision due |
|---|---|---|---|
| OQ-1 | 2026-09-28 | Real-family deployment hosting/privacy model at go-live: keep public repo + public Pages, go private repo + paid Pages, or alternative host (e.g. Vercel/self-host web + Releases for APK)? Demo-only public repo is assumed until then. | Phase 09 (go-live) |
