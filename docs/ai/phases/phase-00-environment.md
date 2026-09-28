# PHASE 00 — Environment setup

**Goal:** verified Flutter + Supabase dev environment on Windows 11; test app ran on the physical
phone and in Chrome; `hfos-demo` Supabase project exists.
**Reference:** `docs/SETUP-GUIDE.md` (step-by-step instructions per stage).
**Out of scope:** any app code, any repo creation, the real family Supabase project.

## Tasks (task IDs match PROGRESS.md board)

### t00.1 — Prep + accounts
- Developer Mode ON; `C:\src`, `C:\dev` created; GitHub + Supabase accounts exist.
- **Acceptance:** `Test-Path C:\src, C:\dev` → True True; user can log into both accounts.

### t00.2 — Git
- **Acceptance:** `git --version` prints; `git config --global user.email` returns owner's email;
  `git config --global init.defaultBranch` → `main`.

### t00.3 — Flutter SDK
- `C:\src\flutter` exists; `C:\src\flutter\bin` on user PATH.
- **Acceptance:** NEW PowerShell: `flutter --version` → `Flutter 3.x • channel stable` + Dart 3.x.

### t00.4 — Android Studio + SDK
- Studio installed; SDK at `%LOCALAPPDATA%\Android\Sdk`; **Command-line Tools (latest)** installed.
- **Acceptance:** folder `%LOCALAPPDATA%\Android\Sdk\cmdline-tools` exists.

### t00.5 — flutter doctor + licenses
- **Acceptance:** `flutter doctor` shows ✓ Flutter, ✓ Android toolchain, ✓ Chrome, ✓ Android
  Studio, ✓ VS Code. (`Visual Studio` ✗ is acceptable.)
- Paste full output into the verification report.

### t00.6 — VS Code
- Extensions: Flutter (with Dart), Error Lens, Material Icon Theme (+ GitLens optional).
- Settings JSON merged per guide Stage 6.
- **Acceptance:** `Ctrl+Shift+P` → "Dart: Open Extension Diagnostics" works; format-on-save active.

### t00.7 — Phone via USB
- **Acceptance:** `flutter devices` lists the physical Android device (not just Chrome/Windows).

### t00.8 — Smoke test (Amendment A-01 — no USB debugging)
- **Acceptance:** `flutter create hello_check` ran; `flutter run -d chrome` showed the counter app
  in Chrome; hot reload (`r`) worked; test folder deleted after.
- Phone-side app validation is **deferred to Phase 01** (APK installed via GitHub Releases or USB
  file-copy/MTP — Developer Options stays OFF on family phones per the banking-app constraint).

### t00.9 — Supabase CLI + demo project
- **Acceptance:** `supabase --version` prints; `supabase login` completed; dashboard shows project
  `hfos-demo` in **Singapore**; URL + anon key + service_role key + DB password stored in owner's
  safe place (NOWHERE in the repo, NOWHERE in chat).

## Phase 00 exit criteria
All t00.x verified with evidence; PROGRESS.md board fully `[x]`; verification log has one row per
task. Then MASTER-SPEC authoring begins (architect-led), and phase-01 is authored from it.
