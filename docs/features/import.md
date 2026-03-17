# Import

The Import feature lets you bring transactions into OtterFin from files exported by other applications or your bank.

## Supported formats

**QIF (Quicken Interchange Format)** — the primary import format. Most personal finance applications and many banks support QIF export, including Quicken, Moneydance, and others.

## Importing transactions

1. Navigate to **Import** in the sidebar
2. Choose the account you're importing into
3. Upload your QIF file
4. Review the transaction preview — OtterFin shows you exactly what will be imported before anything is written
5. Confirm to complete the import

The preview step lets you catch problems — wrong account, duplicate transactions, malformed dates — before they affect your data.

## Import history

Every completed import is recorded in your import history. You can review past imports to see what was brought in and when. This is useful if you're unsure whether a particular statement has already been imported.

## Avoiding duplicates

OtterFin does not automatically deduplicate imported transactions against existing ones. If you import the same file twice, or import a date range that overlaps with transactions you've already entered manually, you may end up with duplicates.

Before importing, check the date range of your file and compare it against what's already in the target account.

## Migrating from Banktivity

If you're moving your full financial history from Banktivity, the [Banktivity importer](../migrating/banktivity.md) is a more complete path than QIF — it handles accounts, categories, transfers, and opening balances in one operation.
