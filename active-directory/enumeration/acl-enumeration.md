---
description: AD ACL Enumeration
---

# ACL Enumeration

### Explanation

Access Control Manager works with 2 parts:

1. **Access Token** (have the identity and permissions of the principal).
2. **Security Descriptor** (have the SID of the owner, DACL and SACL).

When a process wants to access any object in a domain, it presents it's access token to that object. The object have the following security descriptors to check if it can grant access to that process:

* **DACL**: List of permissions that shows who have permissions to access that object.,
* **SACL**: Controls the audit policy of that object (success or failure of a particular right).

**Note**: Both contains ACEs (Access Control Entities), which are the individual permissions on each DACL and SACL.

### Get the ACLs associated with an specific object

```bash
#PowerView:
Get-ObjectACL -SamAccountName $OBJECT_NAME -ResolveGUIDs
```

### Get the ACLs associated with an specified ADS prefix

```bash
#PowerView:
Get-ObjectACL -ADSprefix 'CN=Domain Admins,CN=Users" -Verbose
```

### Get the ACLs associated with an specific LDAP path

{% code overflow="wrap" %}
```bash
#PowerView:
Get-ObjectAd -ADSpath 'LDAP://CN=Domain Admins,CN=Users,DC=dollarcorp,DC=moneycorp,DC=local' -ResolveGUIDs -Verbose
```
{% endcode %}

### Search for interesting ACEs (where we have write/modify permissions)

{% code overflow="wrap" %}
```bash
#PowerView:
Invoke-ACLScanner -ResolveGUIDs
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match- "$USERNAME"} #Enumerate only for a current user, if it doesn't work, try for the groups he have access.
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match- "RDPUsers"} #Query a specific group for interesting ACEs
```
{% endcode %}

### Get the ACLs associated for an specific path

```bash
#PowerView:
Get-PathAcl -Path '\\dcorp-dc.dollarcorp.moneycorp.local\sysvol'
```
