# Household Finance OS

A private, cash-first household finance operating system for one family — and a public,
fictional-data demo of that same system. Built with Flutter (Android + web) and Supabase
(Postgres, RLS, Auth, Storage, Edge Functions).

**Status:** active MVP development. Builds in [Releases](https://github.com/divathed3vil-sys/household-finance-os/releases)
and the [live web build](https://divathed3vil-sys.github.io/household-finance-os/) run against a
demo project with fictional data only.

## What it does (MVP scope)

- Cash-first tracking: physical wallets per person, bank accounts, transfers that never count as income/expense
- Low-friction expense entry: smart autocomplete, split expenses, receipt photos
- Monthly budgets from templates; allocated / spent / remaining always computed
- Household timeline, Appa dashboard, personal analytics
- Append-only ledger + audit log: every rupee explainable
- Offline-first capture with idempotent sync
- Role-based access enforced by Postgres Row-Level Security

## Documentation

The spec is law — everything is documented before it is built:

- `docs/MASTER-SPEC.md` — master technical specification
- `docs/ai/` — build-control system (context, rules, phase task files, progress tracker)
- `docs/SETUP-GUIDE.md` — dev environment setup (Windows 11)

## Tech

Flutter · Dart · Supabase · GitHub Actions (APK → Releases, web → Pages)
