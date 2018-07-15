## SQL Server - How to shrink log of mirrored database?

Quick tip for SQL 2008 R2:

1. Full backup your database.

2. Backup transaction logs.

3. Check log file status:

```sql
DBCC LOGINFO ("YOUR_DB_NAME")
```

4. If you found Status 2 in step 3 run:

```sql
DBCC SHRINKFILE (YOUR_DB_NAME_log, EMPTYFILE);
```

5. Repeat step 3 and 4 until you get Status 0.

6. Shrink transaction logs:

```sql
DBCC SHRINKFILE("YOUR_DB_NAME_log",1)
```