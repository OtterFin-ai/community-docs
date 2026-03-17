# Migrating from Banktivity

OtterFin includes a command-line importer that reads Banktivity's SQLite store directly and migrates your data into OtterFin — accounts, categories, transactions, transfers, and opening balances.

## What gets imported

| Data | Imported |
|------|----------|
| Accounts (checking, savings, credit, etc.) | Yes |
| Account folders (from Banktivity account groups) | Yes |
| Categories (income and expense) | Yes |
| Transactions | Yes |
| Transfers between accounts | Yes |
| Opening balances | Yes |
| Balance adjustments | Yes |
| Tags | Yes (when discoverable in the SQLite schema) |
| Budgets | No |
| Investments and securities | No |
| Goals | No |
| Attachments | No |

## Before you begin

The importer requires access to the source code and development tooling (Node.js, pnpm). If you are running OtterFin from a pre-built Docker image, you will need to clone the OtterFin repository on a separate machine to run the import.

Prerequisites:

- OtterFin cloned from source (`git clone https://github.com/otterfin-ai/otterfin.git`)
- Node.js 25+ and pnpm installed
- A running OtterFin instance with a household already created
- Your Banktivity file — specifically the unencrypted SQLite store at `StoreContent/core.sql` inside your Banktivity document

!!! warning "Close Banktivity first"
    The SQLite file can be locked while Banktivity is open and writing to it. Quit Banktivity before running any importer commands.

## Step 1: Build the importer

```bash
pnpm run build:scripts
```

This compiles the TypeScript importer source into runnable JavaScript in `dist/scripts/`.

## Step 2: Locate your Banktivity file

Your Banktivity document is typically a `.banktivity8` (or `.banktivity7`) file in your Documents folder. Right-click it in Finder and choose **Show Package Contents**, then navigate to `StoreContent/core.sql`.

That `core.sql` path is what you'll pass to the importer.

## Step 3: Analyze your Banktivity file

Run the analyzer to see what the importer finds in your file before touching your OtterFin database:

```bash
pnpm run banktivity:analyze -- "/path/to/core.sql"
```

This produces a summary of accounts, categories, and transaction counts. No database connection is required at this step.

## Step 4: Find your household and user IDs

You'll need your OtterFin household ID and user ID for the next steps. You can find these in the OtterFin admin panel, or by querying the database directly:

```bash
docker exec -it otterfin-postgres psql -U otterfin -d otterfin \
  -c "SELECT id, name FROM households;" \
  -c "SELECT id, email FROM users;"
```

## Step 5: Run a dry run

Before importing anything, run the dry-run command. This shows exactly what the importer plans to create — folders, accounts, categories, and transaction counts — without writing anything to your database.

```bash
pnpm run banktivity:dry-run -- "/path/to/core.sql" \
  --household-id=<household-id> \
  --user-id=<user-id> \
  --yes
```

Review the plan carefully. If anything looks wrong — unexpected account names, missing categories, wrong transaction counts — do not proceed until you understand the discrepancy.

## Step 6: Run the import

When the dry-run output looks correct, run the real import:

```bash
pnpm run banktivity:import -- "/path/to/core.sql" \
  --household-id=<household-id> \
  --user-id=<user-id> \
  --yes
```

The importer creates all mapped records and writes an `external_mappings` table that lets you safely re-run the import if needed — already-imported transactions are skipped rather than duplicated.

!!! note "Interactive mode"
    Omitting `--household-id` and `--user-id` causes the CLI to prompt interactively instead.

## How accounts are mapped

| Banktivity class | OtterFin type |
|-----------------|---------------|
| 1000, 1001, 1002, 1003, 1006 | Asset |
| 4001, 5001 | Liability |
| 6000 | Income category |
| 7000 | Expense category |

Account folders are created from Banktivity account groups when those groups contain accounts.

## How transactions are mapped

- **Two real-account lines** → OtterFin transfer
- **One real-account line + category lines** → OtterFin transaction, with splits when multiple categories are present
- **Foreign currency transactions** — the importer converts the Banktivity line amount to the account currency using the stored Banktivity exchange rate, and preserves the original currency, original amount, and exchange rate in OtterFin's foreign currency fields

## Idempotency

The importer uses OtterFin's `external_mappings` table to remember how Banktivity entities map to OtterFin records. After a successful import, running the importer again against the same household skips already-imported transactions and transfers.

!!! warning "Partial failures"
    If an import run fails before the mapping records are written, partial data may exist in the database. Check the dry-run output again and review what was and wasn't created before re-running.

## Known limitations

- Banktivity uses line-item cleared state; OtterFin stores a single transaction status — the import resolves this but there may be edge cases
- Account and category matching before mappings exist is name/path based, so renamed accounts may not match correctly on a re-import
- The importer targets core household finance data only, not Banktivity's full feature set

## After the import

Once the import completes, sign into OtterFin and verify:

1. Account balances match your Banktivity totals
2. Transaction history looks correct
3. Transfers appear as linked transfers between accounts
4. Categories and tags are assigned as expected

If you find discrepancies, check the importer logs for warnings, and refer to the dry-run output for what was planned versus what was expected.
