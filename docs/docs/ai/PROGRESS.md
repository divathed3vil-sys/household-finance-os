# PROGRESS — living build tracker

**Current position:** Phase 01 ✅ complete · MASTER-SPEC Phases 1–3 ✅ **APPROVED by owner
2026-09-29** (visibility default 'shared' ratified) · Phase 4 (Security) delivered this session ·
next: owner review of Phase 4 → phase-02 build file authored from Phases 3+4. Doc-sync pending
(owner commits P3+P4 together when told).
**How to update:** verification log gets one row per completed task; checkboxes flip only with
evidence (command output). The human commits after each verified task.

## Status legend
`[ ]` pending · `[~]` in progress · `[x]` done + verified · `[!]` blocked (note why)

## Phases

| Phase | Name | State |
|---|---|---|
| 00 | Environment setup | **done ✅** |
| 01 | Project scaffold + Supabase-free repo + GitHub CI | **done ✅** (692414f) |
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

- [x] t01.1 Flutter scaffold `household_finance_os` (org com.hfos, android+web) — 42 files; Chrome counter verified; analyze 0 issues
- [x] t01.2 Control docs imported from docs-control-pack.zip (byte-verified vs architect masters) + README
- [x] t01.3 git init (main) + hardened .gitignore + first commit `1b3cfbb` (44 files, tree clean)
- [x] t01.4 Public repo `household-finance-os` created + pushed (73 objects; tracking set)
- [x] t01.5 build.yml CI green (`576c490`): APK → "Dev build (latest)" release; Web → Pages — both **independently verified by architect via anonymous fetches**
- [x] t01.6 Phone install from Releases (Samsung A546E, Dev Options OFF, no login) + Pages loads — closes deferred t00.8
- [x] t01.7 Tracker close-out commit `692414f` + pushed + architect report

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
| 2026-09-28 | DeepSeek + owner | t01.1 | PASS | "Wrote 42 files"; counter in Chrome; flutter analyze 0 issues; Flutter 3.47.5/Dart 3.13.4 matches baseline; full report in architect chat |
| 2026-09-28 | DeepSeek + owner | t01.2 | PASS | docs/ 10 files at repo root; README replaced; analyze clean 7.6s; **byte-corroborated by architect** (MASTER-SPEC 25,681 B + phase-01 6,433 B = exact match); git-status check dropped (see note) |
| 2026-09-28 | DeepSeek + owner | t01.3 | PASS | Fresh init on main; first commit `1b3cfbb` (44 files, 2154 insertions); secrets block in .gitignore; tree clean; CRLF/wrapper-jar/mojibake observations ratified non-actionable |
| 2026-09-28 | DeepSeek + owner | t01.4 | PASS | Public repo created, no templates; pushed `1b3cfbb`; docs/+lib/ remote; silent cached auth. Notes: gate output not pasted (ratified retroactively; evidence-reporting tightened); secret-scanning attestation moved to t01.5 |
| 2026-09-28 | DeepSeek + owner + architect | t01.5 | PASS | `576c490`; both jobs green; release `dev-latest` w/ app-debug.apk (143 MB fat debug — expected, estimate-error owned by agent); Pages live. **Architect independently verified** Pages + public Releases via anonymous fetch. ⚠ Code-security toggles still unattested (2nd miss) → mandatory in t01.6 evidence |
| 2026-09-28 | DeepSeek + owner | t01.6 + t01.7 | PASS | Phone install clean (A-01 honored: Dev Options OFF, anonymous download, one-time unknown-apps grant); counter verified on Samsung + Pages; close-out `692414f` pushed; phase exit criteria all met; NFR traceability table clean. Note: Code-security attestation STILL unreported (3rd) — owner eyeball backloged + live push-protection test scheduled Phase 09 |
| 2026-09-29 | owner + architect | Spec Phase 3 APPROVED | GATE CLEARED | Visibility default 'shared' ratified (private escape per entry); all other Phase-3 defaults stand. Phase 4 (Security) authored immediately after. |
| 2026-09-28 | owner + architect | A-01 dev-loop amendment; sharing policy; anti-drift protocol; decision №13; OQ-1 | LOGGED | MASTER-SPEC appendix; AI-CONTEXT; AI-SHARING-POLICY.md |

## Backlog (discovered follow-ups — NOT current work)

| Item | Raised by | Phase |
|---|---|---|
| OQ-1: go-live hosting/privacy model (public vs private repo; Pages vs alt host) | architect | 09 |
| Silence cosmetic `[!] Android Studio (not installed)` if desired | owner env choice | 09 |
| Set `redhat.telemetry.enabled: false` in VS Code settings | architect | 02 |
| App signing (release keystore) before wider distribution | architect | 09 |
| Optional: install PowerShell 7 (`winget install Microsoft.PowerShell`) for clean UTF-8 console output (PS 5.1 shows UTF-8 files as mojibake in console; files themselves are correct) | DeepSeek | 09 |
| Optional: `.gitattributes` with `* text=auto eol=lf` if cross-platform LF consistency ever becomes a need | t01.3 obs. | 09 (or never) |
| Switch CI APK build to `--split-per-abi` to shrink debug APK (~143 MB fat → ~50–60 MB/device; release ~10 MB) | t01.5 obs. | 09 |
| Rebrand default Flutter web title ("Flutter Demo") when UI system lands | architect | 06 |
| ⚠ STANDING RULE: architect-mandated steps each require their own attestation row in the evidence pack — no bundling, no silence | architect (2nd miss on code-security toggles) | all |
| Owner 30-sec eyeball: Settings → Code security → Secret scanning + Push protection status (attest in next report); PLUS live push-protection proof-test during security testing | architect (3rd miss) | 02 / 09 |
| doc-sync ritual: repo `docs/` is canonical after t01.2; workspace is architect's authoring copy — owner re-copies changed docs and commits `docs: sync` when told | architect | continuous |
