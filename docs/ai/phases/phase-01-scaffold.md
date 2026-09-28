# PHASE 01 — Project scaffold + GitHub repo + CI distribution

**Goal:** an empty-but-real Flutter app (runs on Chrome), living in a public GitHub repo together
with the `docs/` control system, with CI that publishes a **debug APK to a GitHub Release** and the
**web build to GitHub Pages** on every push to `main`. Phase ends with the counter-app installed on
the owner's physical phone **from the Releases page** (closes the deferred t00.8 phone check).

**Spec refs** (paste these along with this file — no other MASTER-SPEC sections needed for Phase 01):
- Decisions №11 (GitHub Releases + Pages distribution), №12 (no USB debugging; A-01), №13 (public
  repo, demo-project-only, OQ-1 deferred) — all in `docs/ai/AI-CONTEXT.md`.
- `NFR-PRT-01` one codebase ships Android APK (Releases) + Web (Pages).
- `NFR-SEC-04` service_role key never in client/git; anon key is public-by-design.
- Machine facts (Phase 00 verified): Flutter 3.47.5 stable · JDK 17 · cmdline-tools-only Android SDK
  · Windows 11 · Git 2.55 with Credential Manager.

**Absolute rules for this phase**
- Repo name EXACTLY `household-finance-os`, **public**, owner `divathed3vil-sys`.
- Do NOT add Supabase packages, keys, or `.env` anything yet — that is Phase 02's job.
  If a step seems to need a secret, STOP and ask.
- Do NOT edit anything under `docs/` — copy it verbatim (architect owns it).

---

### t01.1 — Flutter scaffold
```powershell
cd C:\dev
flutter create household_finance_os --org com.hfos --platforms=android,web
cd household_finance_os
flutter run -d chrome     # counter app appears; press q
```
- **Acceptance:** project exists; counter runs in Chrome; `flutter analyze` shows no issues.

### t01.2 — Import the control docs + README
- Download `docs-control-pack.zip` (from the architect workspace), extract so the repo root has
  `docs/` alongside `lib/` (`docs/MASTER-SPEC.md`, `docs/ai/...` — verbatim, unchanged).
- Root `README.md`: project title, one-paragraph description, "documentation lives in `docs/`",
  build-instructions pointer to `docs/SETUP-GUIDE.md`. Public-repo appropriate (no secrets, no
  real family data).
- **Acceptance:** `Test-Path docs\MASTER-SPEC.md, docs\ai\PROGRESS.md` → True True.

### t01.3 — Git init + hardened .gitignore + first commit
- To the Flutter-generated `.gitignore`, append:
  ```
  # secrets — never commit
  .env
  .env.*
  *.env
  *.keystore
  supabase/.branches
  supabase/.temp
  ```
- `git init` (defaults to `main`), `git add -A`, `git commit -m "phase-01: flutter scaffold + control docs"`.
- **Acceptance:** `git log --oneline` shows the commit; `git status` clean; GitHub Desktop-less env
  uses existing Credential Manager.

### t01.4 — Create the public GitHub repo and push
- On github.com → New repo → name `household-finance-os`, **Public**, no template files ("Do NOT
  initialize with README/license" — repo already has content).
- Connect + push:
  ```powershell
  git remote add origin https://github.com/divathed3vil-sys/household-finance-os.git
  git branch -M main
  git push -u origin main
  ```
  Browser auth popup (Credential Manager) → approve.
- **Acceptance:** repo page shows `docs/` and `lib/` on GitHub.

### t01.5 — CI: APK → Release, Web → Pages
- Create file `.github/workflows/build.yml` with EXACTLY this content:
```yaml
name: build-and-distribute
on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: write
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  apk:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: "17"
      - uses: subosito/flutter-action@v2
        with:
          channel: stable
          cache: true
      - run: flutter pub get
      - run: flutter build apk --debug
      - uses: softprops/action-gh-release@v2
        with:
          tag_name: dev-latest
          name: "Dev build (latest)"
          prerelease: true
          files: build/app/outputs/flutter-apk/app-debug.apk

  web:
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - uses: actions/checkout@v4
      - uses: subosito/flutter-action@v2
        with:
          channel: stable
          cache: true
      - run: flutter pub get
      # Phase 02 will add: --dart-define=SUPABASE_URL=${{ secrets.SUPABASE_URL }}
      #                    --dart-define=SUPABASE_ANON_KEY=${{ secrets.SUPABASE_ANON_KEY }}
      - run: flutter build web --release --base-href /household-finance-os/
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: build/web
      - id: deployment
        uses: actions/deploy-pages@v4
```
- **One manual step (owner, in repo Settings):** Settings → Pages → Build and deployment →
  Source: **GitHub Actions**.
- Commit + push; open the repo's **Actions** tab and watch both jobs go green (~5–8 min first run).
- **Acceptance:** Actions run green; a release named **"Dev build (latest)"** exists containing
  `app-debug.apk`.

### t01.6 — Phone install test (closes deferred t00.8)
- On the **phone's browser**: open
  `https://github.com/divathed3vil-sys/household-finance-os/releases`
  (public repo → no login needed) → download `app-debug.apk` → open it → allow "install unknown
  apps" for the browser when asked (one-time) → Install → Open → counter app runs on the Samsung.
- Also open the Pages URL in any browser:
  `https://divathed3vil-sys.github.io/household-finance-os/` → app loads.
- **Acceptance:** owner reports "installed from Releases, runs on phone" + "Pages loads".
- Developer Options stays OFF throughout (Amendment A-01).

### t01.7 — Phase close-out
- Update `docs/ai/PROGRESS.md` (tasks → [x], verification-log rows, current position → Phase 02)
  locally in the repo; commit as `phase-01: close-out (tracker)`.
- **Acceptance:** pushed; architect notified with the report.

## Phase 01 exit criteria
Chrome-run app · public repo with docs · green CI · APK on physical phone via Releases · Pages live
· tracker synced. Next: architect delivers MASTER-SPEC Phase 3 (Data Architecture); Phase 02
(auth + app lock) is authored from it — no app code beyond scaffold until then.
