Good question — **looking at the filesystem (`postgres_data/data/`) is NOT enough** to understand *which tables* are using space. PostgreSQL stores data in internal files, so the **correct way is via SQL**.

Below is the **proper, production-grade way** to measure PostgreSQL storage usage.

---

## 1️⃣ Connect to PostgreSQL

If this is Docker:

```bash
docker exec -it <postgres_container> psql -U postgres -d family_spending
```

Or locally:

```bash
psql -U postgres -d family_spending
```

---

## 2️⃣ Total database size

```sql
SELECT
  pg_size_pretty(pg_database_size(current_database())) AS db_size;
```

Example:

```
 db_size
---------
 2.3 GB
```

---

## 3️⃣ Size per schema (very useful for Django)

```sql
SELECT
  nspname AS schema,
  pg_size_pretty(SUM(pg_total_relation_size(pg_class.oid))) AS size
FROM pg_class
JOIN pg_namespace ON pg_namespace.oid = pg_class.relnamespace
WHERE nspname NOT IN ('pg_catalog', 'information_schema')
GROUP BY nspname
ORDER BY SUM(pg_total_relation_size(pg_class.oid)) DESC;
```

Typical Django output:

```
 schema | size
--------+-------
 public | 1.9 GB
```

---

## 4️⃣ Size per table (MOST IMPORTANT)

This shows **table + indexes + TOAST**:

```sql
SELECT
  relname AS table,
  pg_size_pretty(pg_total_relation_size(relid)) AS total_size,
  pg_size_pretty(pg_relation_size(relid)) AS table_only,
  pg_size_pretty(pg_indexes_size(relid)) AS indexes
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC;
```

Example:

```
 table           | total_size | table_only | indexes
-----------------+------------+------------+---------
 orders          | 820 MB     | 410 MB     | 380 MB
 order_items     | 610 MB     | 390 MB     | 190 MB
 django_session  | 120 MB     | 120 MB     | 0
```

👉 **This immediately tells you what eats disk**

---

## 5️⃣ Find tables with huge TOAST (large JSON, text, blobs)

```sql
SELECT
  relname,
  pg_size_pretty(pg_total_relation_size(relid) -
                 pg_relation_size(relid) -
                 pg_indexes_size(relid)) AS toast_size
FROM pg_catalog.pg_statio_user_tables
ORDER BY
  (pg_total_relation_size(relid) -
   pg_relation_size(relid) -
   pg_indexes_size(relid)) DESC;
```

If toast is huge → likely:

* JSONField
* TextField
* logs / audit tables

---

## 6️⃣ Index bloat check (common in Django)

```sql
SELECT
  relname AS table,
  pg_size_pretty(pg_indexes_size(relid)) AS index_size
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_indexes_size(relid) DESC;
```

If index size ≈ table size → **bloat**, run:

```sql
VACUUM (ANALYZE);
REINDEX DATABASE family_spending;
```

---

## 7️⃣ Mapping filesystem to DB (optional, advanced)

Your directory:

```
postgres_data/data/base/
```

Each subdirectory = **database OID**

Find OID:

```sql
SELECT oid, datname FROM pg_database;
```

Then:

```bash
sudo du -sh base/<OID>
```

But ⚠️ **never rely on filesystem only** — SQL is authoritative.

---

## 8️⃣ WAL size (you DO have pg_wal growing)

Check:

```bash
sudo du -sh postgres_data/data/pg_wal
```

Inside Postgres:

```sql
SHOW max_wal_size;
SHOW min_wal_size;
```

Large WAL often means:

* long transactions
* replication slots
* missing vacuum

---

## 🔥 Recommended quick workflow for you

Run **only these first**:

```sql
SELECT pg_size_pretty(pg_database_size(current_database()));
```

```sql
SELECT relname, pg_size_pretty(pg_total_relation_size(relid))
FROM pg_catalog.pg_statio_user_tables
ORDER BY pg_total_relation_size(relid) DESC
LIMIT 10;
```

Paste the output here — I’ll tell you **exactly what to clean, archive, or optimize** (especially for Django models like `Order`, `Purchase`, logs, history tables).
