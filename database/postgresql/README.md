
# PostgreSQL

## Permission's

### Role

```sql
-- Create role
CREATE ROLE "[ROLE NAME]";

-- Allow connection
GRANT CONNECT ON DATABASE [DATABASE] TO [ROLE NAME];

-- Assign your user to role
GRANT "[ROLE NAME]" TO "[USERNAME]";
```

## Query's

### Read Only Permission

```sql
-- Grant usage on schema
GRANT USAGE ON SCHEMA [SCHEMA] TO "[USERNAME]";

-- Grant SELECT on all existing tables
GRANT SELECT ON ALL TABLES IN SCHEMA [SCHEMA] TO "[USERNAME]";

-- Make future tables automatically readable
ALTER DEFAULT PRIVILEGES IN SCHEMA [SCHEMA] GRANT SELECT ON TABLES TO "[USERNAME]";

-- Grant access to sequences
GRANT USAGE, SELECT ON ALL SEQUENCES IN SCHEMA [SCHEMA] TO "[USERNAME]";
ALTER DEFAULT PRIVILEGES IN SCHEMA [SCHEMA] GRANT USAGE, SELECT ON SEQUENCES TO "[USERNAME]";
```

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
