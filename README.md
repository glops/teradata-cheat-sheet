# Teradata Cheat Sheet

## [Explain Plan](Explain_plan)
## [Timezone](Timezone)

## Query logging

### Activate basic query logging  :

```sql
replace query logging on my_database;
```

### More options : 

```sql
replace query logging 
    with objects, sql, explain, stepinfo, 
        usecount, detailed statsusage, 
        verbose xmlplan, paraminfo
	on my_database;
```

### Flush query logging data from memory to table 

```sql
FLUSH QUERY LOGGING WITH ALL;
```


## Statistics

### Show detailed statistics on a column

```sql
show statistics values column id_filename on my_database.my_table ;
```

## Java

### Restart JVM on TD nodes

```sql
call SQLJ.ServerControl('JAVA', 'disable', a);
call SQLJ.ServerControl('JAVA', 'shutdown', a);
call SQLJ.ServerControl('JAVA', 'status', a);
call SQLJ.ServerControl('JAVA', 'enable', a);
```

## Spool usage


## Casting

### Cast CLOB to VARCHAR

If the data contains in clob is bigger than 64k (32k unicode) it cannot be cast directly to varchar.

First substr the first 32/64k characters then cast:

```sql
Select cast(substr(clobfield, 1, 32000) AS VARCHAR(32000))
from my_database.my_table;
```

### Cast JSON to VARCHAR

Same as for CLOB except that substr does not work on JSON.

First cast to CLOB, substr the first 32/64k characters then cast to VARCHAR

```sql
Select CAST(substr(cast(jsonField as CLOB), 1, 32000) AS VARCHAR(32000))
from my_database.my_table;
```

## Skew and data distribution

### Check the distribution of a table

```sql
select 
      hashamp(hashbucket(hashrow(<primary index>))) as amp
    , count(*)
from my_database.my_table
group by 1
order by 2 desc;
```

## Show create statements of all views and tables involved in a query

This will returns also the DDL of the tables which are referenced in a foreign key

It is especially usefull to retrieve objects used in imbricated views.

```sql
show select * from my_database.my_table;
```

Will returns one result set per create statement.

# Metadata

## Get the metadata for the column of a view

When querying DBC.ColumnsV for a view, only the name and comment are available.

To get all the information, use:

```sql
help column DBC.QryLogV.*;
```

Or for a set of columns:

```sql
help column DBC.QryLogV.ProcID,DBC.QryLogV.CollectTimeStamp,DBC.QryLogV.QueryID;
```
