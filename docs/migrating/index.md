# Migrating to OtterFin

OtterFin provides tools and import formats to help you bring your existing financial data over from other applications.

## From Banktivity

OtterFin includes a dedicated Banktivity importer that reads Banktivity's SQLite store directly and maps accounts, categories, transactions, and transfers into OtterFin. This is the most complete migration path available, preserving account structure, transaction history, splits, transfers, and opening balances.

See [Migrating from Banktivity](banktivity.md) for the full step-by-step guide.

## QIF Import

OtterFin supports importing transactions from QIF (Quicken Interchange Format) files, which are exported by many personal finance applications including Quicken, Moneydance, and others.

QIF imports are done through the **Import** section of the app. You upload the file, preview the transactions before they are committed, and OtterFin records the import in your history so you can reference it later.

## Starting fresh

If you are starting with a clean slate, OtterFin's first-run onboarding walks you through creating your household, adding accounts, and entering your opening balances manually.
