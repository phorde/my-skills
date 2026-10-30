---
name: database-migrations
description: Safe, zero-downtime database schema migration patterns. Covers expand-and-contract renames, concurrent indexing, batched backfills, idempotent DDL, and transactional safety across PostgreSQL, SQLite, Prisma, Drizzle, and Go migrations.
---

# Database Migration Patterns

Execute safe, reversible, zero-downtime database schema evolutions.

---

## 1. Core Principles

1. **Every Change is Code**: Never alter database structures manually in production. Every alteration must be an automated, version-controlled migration file.
2. **Forward-Only in Production**: Never execute destructive rollback scripts against live customer data. Rollbacks in production must be applied as new forward migrations.
3. **Separate DDL and DML**: Never combine schema alterations (`CREATE TABLE`, `ALTER TABLE`, `ADD COLUMN`) with massive data backfills (`UPDATE table SET ...`) in the same transaction.
4. **Expand and Contract**: Multi-step zero-downtime deployment for schema changes:
   - **Expand**: Add new columns/tables as nullable. Update code to dual-write.
   - **Backfill**: Asynchronously backfill existing rows in batches.
   - **Contract**: Switch reads to the new structure, remove old writes, and drop old columns in a subsequent release.

---

## 2. PostgreSQL Safety Checklist

- [ ] **Lock Prevention**: Adding a column must either be nullable or carry a `DEFAULT` value (in modern Postgres, constant defaults are metadata-only without table rewrite).
- [ ] **Concurrent Indexing**: Always use `CREATE INDEX CONCURRENTLY` on active production tables outside of transaction blocks to avoid blocking reads/writes.
- [ ] **Drop Columns Safely**: Use `ALTER TABLE ... DROP COLUMN` only after code has completely stopped referencing the field across all running instances.
- [ ] **Foreign Keys**: Add foreign keys with `NOT VALID` first (instant metadata check), then validate concurrently via `VALIDATE CONSTRAINT` to avoid long table locks.

---

## 3. SQLite / Embedded Database Safety

- [ ] **Schema Immutability Workaround**: For operations unsupported by SQLite's limited `ALTER TABLE` (e.g., dropping constraints or changing column types):
  1. Create temporary new table: `CREATE TABLE new_t (...)`.
  2. Copy existing data: `INSERT INTO new_t SELECT ... FROM t`.
  3. Drop old table: `DROP TABLE t`.
  4. Rename new table: `ALTER TABLE new_t RENAME TO t`.
  5. Wrap in an explicit transaction (`BEGIN TRANSACTION ... COMMIT`).
- [ ] **WAL Mode**: Ensure Write-Ahead Logging (`PRAGMA journal_mode=WAL;`) is enabled for concurrent reader/writer stability.

---

## 4. Batched Data Backfills

When backfilling large tables (>10,000 rows):
- Always process in deterministic, indexed batches (e.g., 500 to 1,000 rows by primary key).
- Pause briefly between batches (`sleep`) to let replication lag settle and prevent CPU/IO saturation:
```sql
-- Batch template
UPDATE orders
SET status_code = 1
WHERE id IN (
  SELECT id FROM orders
  WHERE status_code IS NULL
  ORDER BY id ASC
  LIMIT 1000
);
```
