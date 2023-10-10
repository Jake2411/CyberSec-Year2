```SQL
SELECT * FROM (table_name) -- selects all data from a table. * is a wildcard
```

**SELECT INTO example**

```sql
use pubs  --use this database

drop table authors2  --deletes the authors2 table
go

select * into authors2 from authors  --creates authors2 table, copies contents of author
go

select * from authors2 --Shows the result
```

