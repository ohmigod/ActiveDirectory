---
description: AD User Hunting Domain Users Enumeration
---

# User Hunting

> Hunt for specific users or privileges for lateral movement/privesc. Very intrusive, take care.

### Find all machines on the current domain where the current user is local admin

{% code overflow="wrap" %}
```bash
#PowerView:
Find-LocalAdminAccess -Verbose #It looks on all the computers on the domain by:
#1. Requesting a list of all machines to the DC.
#2. Checking if the current user have local admin privileges, by using Invoke-CheckLocalAdminAccess.

Invoke-CheckLocalAdminAccess -ComputerName $COMPUTER_NAME #Only on a single machine
```
{% endcode %}

**Note:** This requires RCP and SMB ports open. If they are blocked, we can use Find-WMILocalAdminAccess.ps1:

{% code overflow="wrap" %}
```bash
. .\Find-WMILocalAdminAccess.ps1
Find-WMILocalAdminAccess -ComputerFile $COMPUTER_FILE.txt -Verbose #Get the list of computers using Get-NetComputer
```
{% endcode %}

### Find local admins on all machines of the domain

**Note:** This requires admin privs on non-dc machines.

```bash
#PowerView:
Invoke-EnumerationLocalAdmin -Verbose #by:
#1. Requests all machines
#2. Get-NetLocalGroup on each machine of the domain
```

### Find computers where a domain admin (or specific user/group) has sessions:

**Note:** If we have local admin privs on that computer we can extract credentials, hashes, tgt's...

{% code overflow="wrap" %}
```bash
#PowerView:
Invoke-UserHunter #Checks for users in the DomainAdmins group
Invoke-UserHunter -GroupName "RDPUsers" #Checks for users in the RDPUsers group
#Steps made by this function:
#1. Get-NetComputer
#2. GetNetGroupMember
#3. List sessions and logged on users on each machine (Get-NetSession & Get-NetLoggedOn)
```
{% endcode %}

We can confirm if we have local admin access with:

```bash
#PowerView:
Invoke-UserHunter -CheckAccess
```

Stealth mode. Same as previous but only on high valuable computers (DC's, fileservers...):

```bash
#PowerView:
Invoke-UserHunter -Stealth #DomainAdmins group users by default
```

### Monitor in real time sessions on a computer

{% code overflow="wrap" %}
```bash
#PowerView:
Invoke-UserHunter -ComputerName $COMPUTER_NAME -Poll 100 -UserName $USERNAME -Delay 5 -Verbose
#Poll: Seconds to monitor.
#Username: Username to monitor.
#Delay: Everytime it checks for a session of that particular user on that particular machine.
```
{% endcode %}
