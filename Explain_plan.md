
# Explain plan

## Activate verbose explain plan for the session:

```sql
diagnostic verboseexplain on for session ;
```

## Activate stats information in explain 

```sql
diagnostic helpstats on for session ;
```

## Dynamic plan

### Show the dynamic plan

```sql
DYNAMIC EXPLAIN
Select ...;
```

## Explain plan in XML

```sql
[DYNAMIC] EXPLAIN IN XML
Select ...;
```

### Force use of dynamic plan

If the dynamic explain starts with:
```
This request is eligible for incremental planning and execution (IPE)
but does not meet cost thresholds. The following is the static plan for the request.
```

The use of dynamic plan can be forced with:

```sql
SET QUERY_BAND = 'DynamicPlan=SYSTEMX;' FOR SESSION;
```

### Force use of static plan

```sql
SET QUERY_BAND = 'DynamicPlan=OFF;' FOR SESSION;
```

### Let the system decide (default)

```sql
SET QUERY_BAND = 'DynamicPlan=SYSTEM;' FOR SESSION;
```
