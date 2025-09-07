
# PostgreSQL

## Query's

### Connections Count

```sql
SELECT usename, COUNT(*) AS total_connections
FROM pg_stat_activity
GROUP BY usename
ORDER BY total_connections DESC;
```

### Replica Identity Full

```sql
SELECT c.relnamespace::regnamespace AS schema_name,
       c.relname AS table_name,
       CASE c.relreplident
           WHEN 'd' THEN 'DEFAULT'
           WHEN 'n' THEN 'NOTHING'
           WHEN 'f' THEN 'FULL'
           ELSE 'UNKNOWN'
       END AS replica_identity
FROM pg_class c
-- Only base tables (not views or other objects)
WHERE c.relkind = 'r'
-- Exclude system tables
AND c.relname NOT LIKE 'pg_%'
AND c.relnamespace NOT IN (
  SELECT oid 
  FROM pg_catalog.pg_namespace 
  WHERE nspname LIKE 'pg_%'
)
ORDER BY c.relnamespace::regnamespace, c.relname;
```
