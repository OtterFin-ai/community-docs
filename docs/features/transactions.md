# Transactions

Transactions are the individual financial events recorded in your accounts — purchases, payments, deposits, transfers, and anything else that changes an account's balance.

## Transaction fields

Every transaction has:

- **Date** — when the transaction occurred
- **Payee** — who you paid, or who paid you
- **Amount** — how much, in the account's currency
- **Category** — what kind of spending or income this represents
- **Status** — cleared or uncleared (reconciled is set through statements)
- **Notes** — optional free-text memo

Optionally:

- **Tags** — one or more tags for cross-category tracking
- **Payment method** — the card or method used
- **Foreign currency** — the original currency and amount if the transaction was in a different currency than the account

## Transaction status

Transactions move through two manually-managed statuses:

- **Uncleared** — entered but not yet confirmed against a bank statement
- **Cleared** — confirmed as posted by your bank

A third status, **Reconciled**, is set when a transaction is matched to a recorded account statement. Reconciled transactions are locked and cannot be edited, preserving the integrity of your statement history.

## Split transactions

A single transaction can be split across multiple categories. This is useful when one payment covers more than one type of spending — a supermarket run that includes groceries, household supplies, and pharmacy items, for example.

Each split line has its own category and amount. The split amounts must sum to the total transaction amount.

## Transfers

A transfer moves money between two accounts you own. OtterFin records a transfer as a linked pair of transactions — a debit in the source account and a credit in the destination account. Transfers are excluded from spending and income category reports so they don't distort your spending picture.

## Foreign currency transactions

If a transaction was made in a currency different from the account's base currency, you can record both the original (foreign) amount and the converted amount that actually posts to your account.

OtterFin stores:

- The original currency and amount (what you actually paid)
- The exchange rate used
- The converted amount in the account's currency (what posts to your balance)

Exchange rates are resolved automatically using your household's configured rates or system rates, with date-aware lookup. You can also enter a manual rate for any transaction.

See [Household](household.md) for details on configuring currencies and exchange rate overrides.

## Viewing transactions

The main **Transactions** view shows all transactions across all accounts you have access to, in reverse chronological order. You can filter by date range, account, category, and status.

Each account also has its own transaction view accessible from the account detail page.
