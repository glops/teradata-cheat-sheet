# Teradata Cheat Sheet

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

## Explain plan

### Activate verbose explain plan for the session:

```sql
diagnostic verboseexplain on for session ;
```

### Activate stats information in explain 

```sql
diagnostic helpstats on for session ;
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
