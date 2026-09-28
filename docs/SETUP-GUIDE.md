# ENVIRONMENT SETUP GUIDE — Windows 11 (Phase 00)

**Machine:** Dell OptiPlex 7040 · i7-6700 · 16 GB RAM · 466 GB SSD · Windows 11 Pro
**Goal:** a fully verified Flutter + Supabase development environment, tested on a real Android phone.
**Time:** ~1.5–2.5 hours, mostly downloads. Total disk needed: ~15 GB.

> **How to use this guide:** Work stage by stage, top to bottom. Do not skip verification commands.
> When a stage says **CHECKPOINT**, report the result before continuing (paste the exact output of
> the command, especially if it shows an error — exact errors are 10x faster to fix than descriptions).

---

## STAGE 0 — Prep (5 minutes)

1. **Enable Developer Mode** (Flutter build tooling needs this):
   - Press `Win + R`, type `ms-settings:developers`, press Enter.
   - Toggle **Developer Mode** → On. Confirm the prompt.
2. **Check free disk space**: Settings → System → Storage. You need ≥ 15 GB free.
3. **Create two folders** (short paths, no spaces, NOT inside OneDrive/Documents):
   - Open PowerShell and run:
     ```powershell
     mkdir C:\src
     mkdir C:\dev
     ```
   - `C:\src` = SDKs (Flutter lives here). `C:\dev` = your projects.

> ⚠️ Do NOT install Flutter into `C:\Program Files` or any OneDrive-synced folder. Both break builds.

---

## STAGE 1 — Accounts (10 minutes, do while later downloads run)

You need two accounts. They are free.

1. **GitHub** — https://github.com/signup
   - Use a personal email you actually read. Username becomes part of your public portfolio later — pick something professional.
2. **Supabase** — https://supabase.com → Sign in → **Continue with GitHub**
   - Using GitHub login links the two accounts (best integration).

> Store all credentials in a password manager or a private note. You will create several secrets
> (DB password, API keys) in later stages — collect them in one safe place.

---

## STAGE 2 — Git (10 minutes)

1. Download: https://git-scm.com/download/win (64-bit, ~70 MB).
   *(Tip: also START the Android Studio download from Stage 4 now — it's the biggest file — and let it download in the background while you continue.)*
2. Run the installer. Defaults are all fine, with two recommended choices:
   - **Choosing the default editor** → select *"Use Visual Studio Code as Git's default editor"*.
   - **Initial branch name** → select *"Override the default branch name for new repositories"* → `main`.
   - Everything else: Next.
3. **Verify** — open a NEW PowerShell window (always open a new window after installing anything):
   ```powershell
   git --version
   # expect: git version 2.x.x.windows.x
   ```
4. **Tell Git who you are** (use your GitHub email):
   ```powershell
   git config --global user.name "Your Name"
   git config --global user.email "you@example.com"
   git config --global init.defaultBranch main
   ```

**✅ CHECKPOINT 1:** `git --version` prints a version.

---

## STAGE 3 — Flutter SDK (20–40 minutes, mostly download)

1. Open https://docs.flutter.dev/get-started/install/windows → click the **stable** ZIP
   (file named like `flutter_windows_3.3x.x-stable.zip`, ~1 GB).
2. When downloaded, right-click the ZIP → **Extract All…** → destination `C:\src`
   → you should end up with the folder `C:\src\flutter` (inside it: `bin`, `packages`, etc.).
3. **Add Flutter to PATH:**
   - `Win + R` → type `sysdm.cpl` → Enter → **Advanced** tab → **Environment Variables…**
   - Under **User variables** select `Path` → **Edit** → **New** → paste:
     ```
     C:\src\flutter\bin
     ```
   - OK, OK, OK.
4. **Verify** — open a NEW PowerShell window:
   ```powershell
   flutter --version
   ```
   First run downloads the bundled Dart SDK — wait for it. Expected output shows
   `Flutter 3.3x.x • channel stable` and `Dart 3.x.x`. Any recent stable version is correct.

**✅ CHECKPOINT 2:** `flutter --version` prints Flutter + Dart versions.

---

## STAGE 4 — Android Studio + Android SDK (30–60 minutes)

We install Android Studio for its **SDK and build tools**. You will write code in VS Code; you may
never open Android Studio again after this.

1. Download: https://developer.android.com/studio (~1.1 GB installer). Run it with defaults.
2. First launch → "Do not import settings" → Setup wizard → **Standard** install type → Finish
   → it downloads the Android SDK (~3 GB) into `C:\Users\<you>\AppData\Local\Android\Sdk`. Wait for it.
3. On the Welcome screen → **More Actions** → **SDK Manager** → open the **SDK Tools** tab:
   - ✅ Tick **Android SDK Command-line Tools (latest)** ← *not ticked by default; required for licenses*
   - ✅ Confirm ticked: *Android SDK Build-Tools*, *Android SDK Platform-Tools*
   - ⬜ Android Emulator — optional; skip for now (you test on a real phone; adds ~1 GB).
   - Click **Apply** → let it install → Finish.

---

## STAGE 5 — flutter doctor + licenses (10 minutes)

Open a NEW PowerShell window:

```powershell
flutter doctor
```

Then accept the Android licenses:

```powershell
flutter doctor --android-licenses
# type y, press Enter, repeat for every license (~5 of them)
```

Re-run `flutter doctor`. Expected result:

```
[√] Flutter (Channel stable, ...)
[√] Windows version
[√] Android toolchain - develop for Android devices
[√] Chrome - develop for the web
[√] Android Studio
[√] VS Code
```

- If **Chrome** shows ✗: install Google Chrome (needed for web development) → https://www.google.com/chrome/
- If **Android toolchain** shows ✗ mentioning licenses: you skipped the Command-line Tools — go back to Stage 4 step 3.
- Ignore ✗ for *Visual Studio* (only needed for Windows-desktop builds, which we don't target).

**✅ CHECKPOINT 3:** `flutter doctor` shows checkmarks for Flutter, Android toolchain, Chrome, Android Studio, VS Code. Paste the full output when reporting.

---

## STAGE 6 — VS Code extensions + settings (10 minutes)

Open VS Code → Extensions panel (`Ctrl+Shift+X`) → install these (and only these for now):

| Extension | Publisher | Why |
|---|---|---|
| **Flutter** | Dart Code | The essential one — auto-installs the Dart extension too |
| **Error Lens** | Alexander | Shows errors inline on the line — huge quality-of-life for beginners |
| **Material Icon Theme** | Philipp Kief | Makes project files visually scannable |
| **GitLens** | GitKraken (optional) | See who changed what, when — useful later |

Then apply editor settings: `Ctrl+Shift+P` → type **Open User Settings (JSON)** → merge in:

```json
{
  "editor.formatOnSave": true,
  "[dart]": {
    "editor.defaultFormatter": "Dart-Code.dart-code",
    "editor.rulers": [100]
  },
  "dart.lineLength": 100,
  "editor.bracketPairColorization.enabled": true,
  "files.autoSave": "afterDelay"
}
```

---

## STAGE 7 — Phone setup: USB debugging (10 minutes)

1. On your Android phone: **Settings → About phone → Software information** → tap **Build number**
   7 times rapidly → "Developer mode has been enabled."
   - *(Xiaomi/Redmi: About phone → tap "MIUI version" 7 times instead.)*
2. **Settings → System → Developer options** → enable **USB debugging**.
3. Connect the phone to the PC with a **data-capable USB cable** (not a charge-only cable!).
4. Unlock the phone — a popup appears: **"Allow USB debugging?"** → tick *"Always allow from
   this computer"* → Allow.
5. **Verify** in PowerShell:
   ```powershell
   flutter devices
   ```
   Your phone should appear in the list (e.g. `SM A346E • android-arm64`), plus Chrome.

**Troubleshooting — phone not listed, in order of likelihood:**
1. Cable is charge-only → try a different cable.
2. The "Allow USB debugging" popup never appeared → unplug/replug with phone unlocked; check the
   notification shade for a USB mode notification and set it to *File Transfer*.
3. Still nothing → Windows Device Manager → look for a device with a ⚠ warning → install the
   manufacturer's USB driver (Samsung/Xiaomi/etc. publish them), or try another USB port (USB 3.0 preferred).

**✅ CHECKPOINT 4:** `flutter devices` lists your phone.

---

## STAGE 8 — Smoke test: run a real app on your phone (15 minutes)

```powershell
cd C:\dev
flutter create hello_check
cd hello_check
flutter run
```

- If multiple devices are listed, select your phone number.
- First build takes 3–10 minutes (it downloads Gradle). End result: the **Flutter counter app
  is running on your physical phone.**
- Tap the + button on the phone — counter increments. In the terminal press **`r`** → hot reload.
- Press **`q`** to quit. Then test the web target:
  ```powershell
  flutter run -d chrome
  ```
  The app opens in Chrome. `q` to quit.
- Delete the test app: `cd C:\dev; Remove-Item -Recurse -Force hello_check`

**✅ CHECKPOINT 5:** you saw the counter app on your phone AND in Chrome.

---

## STAGE 9 — Supabase CLI + demo project (15 minutes)

1. **Install Scoop** (a clean Windows package manager) — PowerShell:
   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   irm get.scoop.sh | iex
   ```
2. **Install the Supabase CLI:**
   ```powershell
   scoop bucket add supabase https://github.com/supabase/scoop-bucket.git
   scoop install supabase
   supabase --version
   ```
3. **Log in the CLI:**
   ```powershell
   supabase login
   ```
   A browser opens with an access token → paste it back into the terminal.
4. **Create the DEMO project** in the Supabase dashboard (https://supabase.com/dashboard):
   - **New project** → Organization: create one named `household` →
     Project name: `hfos-demo` → Region: **Singapore** →
     Database Password: click Generate → **copy it into your safe place immediately**.
   - Wait ~2 minutes for provisioning.
5. In the project → **Project Settings (gear) → API** → record in your safe place:
   - `Project URL` (looks like `https://abcdefgh.supabase.co`)
   - `anon` `public` key (safe to embed in the app)
   - `service_role` `secret` key — **⚠️ this is a master key. It must NEVER go into the app,
     into Git, or into any chat. Dashboard/CLI use only.**

> Do NOT create the real family project yet — that happens in Phase 4 (go-live). We build
> entirely against `hfos-demo`.

**✅ CHECKPOINT 6:** `supabase --version` works; `hfos-demo` exists in the Singapore region; URL + keys recorded safely.

---

## ✅ PHASE 00 COMPLETION CHECKLIST

- [ ] Developer Mode enabled; `C:\src` and `C:\dev` exist
- [ ] GitHub + Supabase accounts created
- [ ] `git --version` ✓ · identity configured
- [ ] `flutter --version` ✓ (stable channel)
- [ ] Android Studio + SDK + Command-line Tools installed
- [ ] `flutter doctor` ✓ (Flutter / Android toolchain / Chrome / VS Code all green)
- [ ] VS Code extensions + settings applied
- [ ] Phone visible in `flutter devices`
- [ ] Counter app ran on phone AND in Chrome
- [ ] `supabase login` done · `hfos-demo` project created in Singapore · keys stored safely

## What we deliberately skipped (and when it comes back)

| Skipped | Why | When added |
|---|---|---|
| Android Emulator | Real phone is better (biometrics, camera) | Optional later for screen-size testing |
| Docker / local Supabase | We develop against the cloud demo project | Only if we need offline backend dev |
| Deno (Edge Functions) | Not needed until Telegram bot | Phase v1.1 |
| Git hooks/CI | Premature | After MVP |

**Next:** `phase-00` gets marked verified in `docs/ai/PROGRESS.md`, then Master Spec sections.
