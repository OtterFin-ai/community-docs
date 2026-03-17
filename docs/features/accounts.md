# Accounts

Accounts are the foundation of OtterFin. Every transaction lives in an account, and your net worth is the sum of all your account balances.

## Account types

OtterFin has two account types:

- **Asset** — accounts that hold value you own: checking, savings, cash, brokerage accounts, and similar
- **Liability** — accounts that represent money you owe: credit cards, loans, lines of credit

Both types track transactions the same way. The distinction matters for net worth calculations: asset balances add to your net worth, liability balances subtract from it.

## Account currency

Each account has its own base currency. An account's balance is always tracked in that currency. If you hold accounts in multiple currencies — a USD checking account and a EUR savings account, for example — OtterFin tracks each independently and converts to your household's base currency for dashboard totals and reports.

## Account folders

Accounts can be organized into folders to keep things tidy. A typical setup might group all bank accounts together and all credit cards together. Folders are for display only — they have no effect on transactions or balances.

## Reconciliation

OtterFin supports statement-based reconciliation. When you receive a bank or credit card statement, you create a statement record in OtterFin and match it against your transactions. Transactions reconciled through a statement are locked to preserve the integrity of that statement.

By default, transactions can only reach **Reconciled** status through a recorded statement. This setting can be changed in household settings if you prefer to mark transactions reconciled manually.

## Account privacy

Account visibility inside a household is controlled per account. When you create an account, you become its owner. You can share it with other household members and assign them an **Editor** or **Viewer** role:

- **Owner** — full control, including managing who else has access
- **Editor** — can add and edit transactions
- **Viewer** — read-only access to the account and its transactions

Accounts you have not been granted access to are not visible to you, even within the same household. This lets household members maintain private accounts for personal spending or savings without sharing them with everyone.

!!! note "Owner override"
    Self-hosted instances can optionally set `HOUSEHOLD_OWNER_IMPLICIT_ACCOUNT_ACCESS=true` so the household owner automatically sees all accounts. This is off by default.

## Account detail

The account detail view shows:

- Current balance
- Full transaction history for that account
- Statements and reconciliation history
- Account settings and sharing configuration
