# PROGRESS — living build tracker

**Current position:** PHASE 01 ✅ COMPLETE — public repo, CI, APK-to-Releases, Pages live, phone
install verified. Next: architect delivers MASTER-SPEC Phase 3 (Data Architecture); Phase 02
(auth + app lock) is authored from it. No app code beyond scaffold until then.
**How to update:** verification log gets one row per completed task; checkboxes flip only with
evidence (command output). The human commits after each verified task.

## Status legend
`[ ]` pending · `[~]` in progress · `[x]` done + verified · `[!]` blocked (note why)

## Phases

| Phase | Name | State |
|---|---|---|
| 00 | Environment setup | **done ✅** |
| 01 | Project scaffold + Supabase-free repo + GitHub CI | **done ✅** |
| 02 | Auth + app lock + session model | pending (awaits spec Phase 3–4) |
| 03 | Core ledger: categories, wallets, outbox, sync | pending |
| 04 | Expense/income/transfer entry + autocomplete + splits + receipts | pending |
| 05 | Budgets: templates, monthly generation, live remaining | pending |
| 06 | Timeline + Appa dashboard + personal views | pending |
| 07 | Audit log + notifications (in-app) | pending |
| 08 | Demo seed generator + demo personas + reseed | pending |
| 09 | MVP hardening + deploy (incl. OQ-1 go-live hosting decision) | pending |
| 10+ | v1.1: vault, trackers, insurance/recurring, Telegram bot, reminders | pending |

## Phase 01 task board — final ✅

- [x] t01.1 Flutter scaffold `household_finance_os` (org com.hfos, android+web), runs on Chrome
- [x] t01.2 Control docs imported from docs-control-pack.zip + README
- [x] t01.3 git init (main) + hardened .gitignore + first commit
- [x] t01.4 Public repo `household-finance-os` created + pushed
- [x] t01.5 build.yml CI green: APK → "Dev build (latest)" release; Web → Pages (Pages source = GitHub Actions)
- [x] t01.6 Phone install from Releases (no login, no USB debugging) + Pages URL loads
- [x] t01.7 Tracker close-out commit + architect report

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
| 2026-09-28 | DeepSeek + owner | t01.1 | PASS | `flutter create` 42 files; counter ran in Chrome (increment confirmed); `flutter analyze` → No issues found (12.8s); Flutter 3.47.5 stable / Dart 3.13.4 |
| 2026-09-28 | DeepSeek + owner | t01.2 | PASS | docs/ 10 files at repo root (Test-Path True True); README replaced (non-default, no secrets); flutter analyze No issues (7.6s); git status check deferred to t01.3 per owner pick (a) |
| 2026-09-28 | DeepSeek + owner | t01.3 | PASS | Fresh `git init` on `main`; secrets block appended to `.gitignore`; first commit `1b3cfbb` (`phase-01: flutter scaffold + control docs`, 44 files, 2154 insertions); working tree clean |
| 2026-09-28 | DeepSeek + owner | t01.4 | PASS | Public repo `divathed3vil-sys/household-finance-os` created (no template files); pushed `1b3cfbb` (73 objects, 86.37 KiB); upstream tracking set; remote shows exactly 1 commit with `docs/` + `lib/`; silent auth via cached Credential Manager creds |
| 2026-09-28 | DeepSeek + owner | t01.5 | PASS | `build.yml` byte-faithful (`576c490`); both CI jobs green; Release `dev-latest` "Dev build (latest)" pre-release with `app-debug.apk` (143 MB fat debug, expected); Pages live at divathed3vil-sys.github.io/household-finance-os/ showing counter app |
| 2026-09-28 | DeepSeek + owner | t01.6 | PASS | Phone installed `app-debug.apk` from Releases (no login, Developer Options OFF throughout); "install unknown apps" granted once; counter runs; Pages URL loads in browser |
| 2026-09-28 | DeepSeek + owner | t01.7 | PASS | Tracker close-out commit `phase-01: close-out (tracker)`; all 7 Phase-01 tasks [x]; architect report delivered |

## Backlog (discovered follow-ups — NOT current work)

| Item | Raised by | Phase |
|---|---|---|
| OQ-1: go-live hosting/privacy model (public vs private repo; Pages vs alt host) | architect | 09 |
| Silence cosmetic `[!] Android Studio (not installed)` if desired | owner env choice | 09 |
| Set `redhat.telemetry.enabled: false` in VS Code settings | architect | 02 |
| App signing (release keystore) before wider distribution | architect | 09 |
| doc-sync ritual: repo `docs/` is canonical after t01.2; workspace is architect's authoring copy — owner re-copies changed docs and commits `docs: sync` when told | architect | continuous |
| Consider `.gitattributes` (`* text=auto eol=lf`) if cross-platform LF consistency ever needed | t01.3 obs. | 09 |
| Optionally switch CI APK build to `--split-per-abi` to shrink debug APK (~143 MB → ~50–60 MB/device) | t01.5 obs. | 09 |