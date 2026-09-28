# CODING RULES — binding for every AI session

## Stack & versions
- Flutter **stable** channel; Dart 3. `supabase-flutter` for backend.
- State management, local DB, routing: per MASTER-SPEC §Technical Architecture. Until that section
  is written and approved, **do not scaffold** feature code — ask.
- No new package dependency without stating it in the task plan and getting a "go".

## Money & financial data
- All money = `int` minor units (cents). Floats for money are a defect. Use the shared `Money` type
  once it exists; until then, raw `int` cents + explicit formatting.
- Never persist derived values (balances, totals, remaining, percentages) as source of truth —
  compute from transactions (DB views/RPC). Cached values must be clearly derived and refreshable.
- Every mutating operation goes through the service/repository layer — no ad-hoc Supabase calls
  scattered in widgets.

## Security
- Authorization lives in **Postgres RLS policies**. Dart code may *also* hide UI, but never
  substitutes for RLS.
- No secrets in code/Git: Supabase URL + anon key via `--dart-define` or untracked `.env`;
  service_role key never ships.
- Audit events are insert-only, written by DB triggers / security-definer functions where the spec
  says so. Client code must not be able to update or delete audit rows.

## Offline
- Every user entry (expense/income/transfer/receipt) is written to the local outbox first, with a
  client-generated UUID idempotency key, and rendered optimistically. No entry flow may block on network.

## Code quality gates (a task is NOT done unless all pass)
- `flutter analyze` — zero issues.
- `dart format .` — applied.
- Tests required by the phase file pass (`flutter test` where applicable).
- App still builds: `flutter build apk --debug` (or runs via `flutter run`).
- Every screen has explicit **loading / empty / error / offline** states per the spec's screen inventory.

## Style
- Feature-first structure under `lib/` (e.g. `lib/features/expenses/`), files `snake_case.dart`,
  classes `UpperCamelCase`, one public widget/class per file where sensible.
- UI copy: English, sentence case, no exclamation marks. LKR formatting via one shared formatter
  (e.g. `Rs. 12,500`).
- Comments explain *why*, especially financial invariants ("// remaining is computed — see spec §6").

## Forbidden (hard rules)
- ❌ Editing `docs/MASTER-SPEC.md` or `docs/ai/AI-CONTEXT.md`.
- ❌ Refactoring code outside the current task's scope.
- ❌ Hard-deleting financial records; storing floats for money; storing computed totals as truth.
- ❌ Adding analytics/tracking/crash-reporting SDKs (privacy:	private household app).
- ❌ Committing `.env`, keys, or any real family data. Demo/seed data must be obviously fictional.
- ❌ "While I was here" changes. Report suggestions in the verification report instead.

## Git conventions
- One task = one commit (human commits, AI proposes the message): `phase-02: expense entry outbox`.
- Work on `main` during solo MVP; feature branches only if the human asks.
