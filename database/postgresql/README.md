
# PostgreSQL

## Query's

### Connections Count

```sql
SELECT usename, COUNT(*) AS total_connections
FROM pg_stat_activity
GROUP BY usename
ORDER BY total_connections DESC;
```
