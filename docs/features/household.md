# Household

A household is the shared financial workspace in OtterFin. All accounts, transactions, categories, budgets, and reports belong to a household. Multiple people can be members of the same household, each with their own login and role.

## Household roles

Every household member has one of three roles:

| Role | Description |
|------|-------------|
| **Owner** | Full access to household settings, can invite and remove members, and manage all configuration |
| **Member** | Can create and manage accounts and transactions they have access to |
| **Viewer** | Read-only access to accounts they have been shared on |

Roles control what you can do at the household level. Access to individual accounts is managed separately — see [Accounts](accounts.md) for per-account privacy.

## Inviting members

Household owners can invite additional members from **Settings → Household**. Invited members receive an email with a link to create their account and join the household.

Once a member joins, they can access any accounts explicitly shared with them.

## Multi-currency

OtterFin supports households that use multiple currencies. The household has a **base currency** — the currency used for totals on the dashboard, net worth calculations, and reports. Individual accounts can be in any currency.

Currencies used in your household are configured in **Settings → Currencies**. For each currency pair you use, you can set manual exchange rate overrides with an optional staleness window. When an override expires, OtterFin falls back to system rates.

Exchange rate resolution follows this priority:

1. Household override (direct or inverse), if within the staleness window
2. System rate (nearest date, direct or inverse)
3. Intermediary rate via USD when a direct pair is unavailable

## Payment methods

Payment methods represent how a transaction was made — a specific credit card, a debit card, cash, and so on. They are optional and used for filtering and reference rather than affecting balances or categories.

Payment methods are managed in **Settings → Payment Methods**.

## Reconciliation policy

By default, a transaction can only reach **Reconciled** status by being matched to a recorded account statement. This prevents accidental reconciliation and preserves the integrity of your statement history.

Household owners can disable this policy in **Settings → Household** by turning off _Require statements to mark transactions reconciled_. When disabled, any transaction can be manually marked reconciled.

## Data management

**Settings → Data** provides options for exporting your household data and managing your data at the household level.
