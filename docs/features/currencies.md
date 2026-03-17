# Multi-Currency

OtterFin supports households that hold accounts and make transactions in multiple currencies. Each account has its own base currency, transactions can be entered in a foreign currency, and everything is normalized to your household's base currency for reports and balances.

## How it works

Each account is denominated in a single currency — a USD checking account, a EUR savings account, and a CAD credit card are all tracked independently in their own currency. Your household has a **base currency** that is used for totals on the dashboard and across all reports.

When OtterFin needs to display a combined balance or a spending total across currencies, it converts each amount to the household base currency using a date-aware exchange rate.

## Recording a foreign currency transaction

When you make a transaction in a currency different from the account's currency — paying in Euros on a USD card, for example — you can record both the original foreign amount and the converted amount that posts to your account.

OtterFin stores:

- The **original currency and amount** — what you actually paid
- The **exchange rate** used for the conversion
- The **converted amount** in the account's base currency — what posts to your balance

This means you always have a record of what you paid in local currency, as well as the exact amount that hit your account.

## Exchange rate resolution

When OtterFin needs a rate and none has been entered manually, it resolves one automatically using this priority:

1. **Household override** — a rate you have set manually for that currency pair, if it is within the staleness window you've configured
2. **System rate** — the nearest available system rate for that date (before or after)
3. **Intermediary rate via USD** — when a direct or inverse pair is unavailable, OtterFin routes through USD to arrive at a rate
4. **Error** — if no valid path exists, the conversion is skipped rather than using a guess

The resolved rate carries metadata you can inspect: the source (direct, inverse, or intermediary), the rate layer (household override or system rate), and the as-of date.

## Household rate overrides

In **Settings → Currencies** you can set manual exchange rate overrides for any currency pair your household uses. This is useful when you want to lock in a specific rate for a period — for example, using the rate from a trip rather than a system rate that may differ.

Each override has an optional **staleness window** (configurable in **Settings → Household**). The default staleness window is 30 days. When an override is older than the window, OtterFin falls back to system rates while still surfacing the stale override in the UI so you can review it.

Set the staleness window to _never expire_ to always use your household override regardless of age.

## Reporting across currencies

The **Spending & Income** report and dashboard totals convert all amounts to the household base currency:

- Each transaction is converted at the rate for its transaction date
- Account balances are converted at the report boundary date
- Transfers are excluded from income and spending totals regardless of currency
- If no exchange rate path exists for a particular conversion, that amount is excluded from the total rather than distorting the result

## Adding currencies

Currencies available in your household are managed in **Settings → Currencies**. Add any currency you hold accounts or make transactions in, and optionally configure rate overrides for it.
