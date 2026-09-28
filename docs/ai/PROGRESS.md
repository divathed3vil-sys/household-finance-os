# PROGRESS — living build tracker

**Current position:** PHASE 00 ✅ COMPLETE. Starting PHASE-01 (scaffold + repo + CI) via first
DeepSeek session — see phases/phase-01-scaffold.md.
**How to update:** verification log gets one row per completed task; checkboxes flip only with
evidence (command output). The human commits after each verified task.

## Status legend
`[ ]` pending · `[~]` in progress · `[x]` done + verified · `[!]` blocked (note why)

## Phases

| Phase | Name | State |
|---|---|---|
| 00 | Environment setup | **done ✅** |
| 01 | Project scaffold + Supabase-free repo + GitHub CI | in progress |
| 02 | Auth + app lock + session model | pending (awaits spec Phase 3–4) |
| 03 | Core ledger: categories, wallets, outbox, sync | pending |
| 04 | Expense/income/transfer entry + autocomplete + splits + receipts | pending |
| 05 | Budgets: templates, monthly generation, live remaining | pending |
| 06 | Timeline + Appa dashboard + personal views | pending |
| 07 | Audit log + notifications (in-app) | pending |
| 08 | Demo seed generator + demo personas + reseed | pending |
| 09 | MVP hardening + deploy (incl. OQ-1 go-live hosting decision) | pending |
| 10+ | v1.1: vault, trackers, insurance/recurring, Telegram bot, reminders | pending |

## Phase 01 task board (detail: phases/phase-01-scaffold.md)

- [ ] t01.1 Flutter scaffold `household_finance_os` (org com.hfos, android+web), runs on Chrome
- [ ] t01.2 Control docs imported from docs-control-pack.zip + README
- [ ] t01.3 git init (main) + hardened .gitignore + first commit
- [ ] t01.4 Public repo `household-finance-os` created + pushed
- [ ] t01.5 build.yml CI green: APK → "Dev build (latest)" release; Web → Pages (Pages source = GitHub Actions)
- [ ] t01.6 Phone install from Releases (no login, no USB debugging) + Pages URL loads
- [ ] t01.7 Tracker close-out commit + architect report

## Phase 00 task board — final ✅

- [x] t00.1 Developer Mode, folders, GitHub + Supabase accounts
- [x] t00.2 Git 2.55.0 + identity; defaultBranch=main
- [x] t00.3 Flutter 3.47.5 stable on PATH
- [x] t00.4 Android SDK cmdline-tools-only `C:\Android\sdk`
- [x] t00.5 SDK 36 + build-tools; licenses; doctor green (VS ✗ expected)
- [x] t00.6 VS Code + Flutter ext + settings
- [x] t00.7 Phone SM A546E (Android 16, API 36) detected — later intentionally disabled (A-01)
- [x] t00.8 Chrome smoke test passed (phone side deferred to t01.6 per A-01)
- [x] t00.9 `supabase login` ✓; `hfos-demo` created (Singapore); keys stored offline

## Verification log

| Date | Agent/model | Task | Result | Evidence / notes |
|---|---|---|---|---|
| 2026-09-28 | DeepSeek + owner | t00.1–t00.4, t00.6, t00.9(part) | PASS (reported) | Owner session summary |
| 2026-09-28 | owner | t00.2fix, t00.5, t00.7 | PASS | `flutter doctor` + `flutter devices` pasted |
| 2026-09-28 | owner | t00.8(Chrome), t00.9 | PASS (reported) | "All done"; keys stored offline, never pasted |
| 2026-09-28 | owner + architect | A-01 dev-loop amendment; sharing policy; anti-drift protocol; decision №13; OQ-1 | LOGGED | MASTER-SPEC appendix; AI-CONTEXT; AI-SHARING-POLICY.md |

## Backlog (discovered follow-ups — NOT current work)

| Item | Raised by | Phase |
|---|---|---|
| OQ-1: go-live hosting/privacy model (public vs private repo; Pages vs alt host) | architect | 09 |
| Silence cosmetic `[!] Android Studio (not installed)` if desired | owner env choice | 09 |
| Set `redhat.telemetry.enabled: false` in VS Code settings | architect | 02 |
| App signing (release keystore) before wider distribution | architect | 09 |
| doc-sync ritual: repo `docs/` is canonical after t01.2; workspace is architect's authoring copy — owner re-copies changed docs and commits `docs: sync` when told | architect | continuous |
