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

CREATE table

```SQL
CREATE (table_name):
	column_name1 data_type; --if you want no null values add NOT NULL after data type
	column_name2 data_type;
	etc...
```

