# Changelog

All notable changes to this documentation site will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Added

- Full documentation structure covering Overview, Installation, Migrating, and Features
- `installation/index.md` — Docker setup, `.env` configuration, first-run onboarding
- `installation/synology.md` — Synology NAS guide for DSM 7.1 and 7.2+ (Container Manager UI and SSH methods, Tailscale, DDNS/reverse proxy, upgrading, troubleshooting)
- `installation/email.md` — email provider configuration (Console, SMTP, Resend) with Docker log instructions and Mailtrap testing guidance
- `migrating/index.md` — migration options overview
- `migrating/banktivity.md` — detailed Banktivity importer walkthrough including account/transaction mapping, idempotency model, and known limitations
- `features/accounts.md` — account types, currency, folders, reconciliation, and per-account privacy
- `features/transactions.md` — transaction fields, status, splits, transfers, and foreign currency entry
- `features/household.md` — household roles, member invites, multi-currency, and reconciliation policy
- `features/budget.md` — budget targets, periods, and budget vs. actual tracking
- `features/reports.md` — Spending & Income report, date ranges, and multi-currency handling
- `features/categories.md` — income/expense category types, hierarchy, and management
- `features/tags.md` — tags vs. categories, use cases, and filtering
- `features/currencies.md` — foreign currency entry, exchange rate resolution order, household overrides, and staleness policy
- `features/import.md` — QIF import workflow, import history, and deduplication guidance
- `features/settings.md` — profile, household, currencies, payment methods, and data settings
- OtterFin brand color scheme via `docs/stylesheets/extra.css` (teal primary, ocean blue accent, warm cream background for light mode; blue-charcoal hue for dark mode)
- Explicit navigation in `zensical.toml` covering all four sections
