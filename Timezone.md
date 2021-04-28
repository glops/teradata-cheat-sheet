# Time zone

## Time zone parameters

Teradata define time zone at three level:

- Server

- User

- Session

### Server Time zone

It is set in /etc/opt/teradata/tdconfig/tdlocaledef.txt

It looks like:

```
Time ZoneString {
    "Europe Central"; "1"; "0"; "2";
    "3"; "3"; "0"; "0"; "-1"; "02:00:00";
    "3"; "9"; "0"; "0"; "-1"; "03:00:00";
    "1987"; "1995"; "1"; "0"; "2"; "0";
    "3"; "3"; "0"; "0"; "-1"; "02:00:00";
    "3"; "10"; "0"; "0"; "-1"; "03:00:00";
    "1996"; "9999"; "1"; "0"; "2"; "0"
}
```

The value can be seen in dbscontrol (but not modified).

### User Time zone

A Time zone can be define for each user. 

If the time zone is not defined, the user will use **server time zone**.

> **Warning: as stated in Teradata documentation, NO specific time should be defined on users.**

#### Define User time Zone

During user creation, you can define a specific time zone using the parameter `Time Zone`.

```sql
create user user_name 
    from  database_name
    as ...
    time zone = 'Europe Central'
```

#### Change Time zone

```sql
modify user user_name as time zone = 'Europe Central';
-- or 
modify user user_name as time zone = '+01:00';
-- Remove user specific time zone
modify user user_name as time zone = null;
```

#### Show user time zone

```sql
select 
    username,
    TimeZoneHour,
    TimeZoneMinute,
    TimeZoneString 
from "DBC".UsersV
```
The time zone is defined either in TimeZoneHour + TimeZoneMinute or in TimeZoneString

### Session time zone

```sql
set time zone 'Europe Central';
-- or
set time zone '+01:00';
-- Set time zone to server value (not user value !)
set time zone local;
```

### Show session time zone

```sql
select TimeZoneString from "DBC".SessionInfoVX where SessionNo = session
```

## List of the time zones

[List from Teradata documentation](https://docs.teradata.com/r/w19R4KsuHIiEqyxz0WYfgA/67rSW_MQwBiVQ3siO~dt7Q)

## Using time zones

Let's try with examples:

### Timestamp without time zone

```sql
create volatile table tb_without_tz (ts_without_tz timestamp(0)) on commit preserve rows;
-- Here, we assume that the session time zone is the same as the server 'Europe Central' in this case.
ins tb_without_tz values ('2021-04-28 10:00:00');
select * from tb_without_tz;
-- Shows 2021-04-28 10:00:00

set time zone 'America Eastern';
select * from tb_without_tz;
-- Shows 2021-04-28 04:00:00
```

As you can see, even if the field is defined **without** time zone, the output depends on the session time zone. This can be very confusing and that's why it is recommended to keep user/session time zone unset. 

#### Set a time zone different from the server before inserting

```sql
del tb_without_tz;
set time zone 'America Eastern';
ins tb_without_tz values ('2021-04-28 10:00:00');
select * from tb_without_tz;
-- Shows 2021-04-28 10:00:00

-- Set back session time zone to server value
set time zone local;
select * from tb_without_tz;
-- Shows 2021-04-28 16:00:00
```

#### Conclusion

When working on timestamp without time zone:

- when inserting, teradata convert the timestamp from the session time zone to the server time zone

- when selecting, teradata convert the timestamp from the server time zone to the session time zone
