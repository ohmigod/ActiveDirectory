---
description: AD GPO Enumeration
---

# GPO Enumeration

> Extract information from GPO's to see if it can be useful for any abuse later on. GPOs are applied to OUs.

### GPO:

* Provides ability to manage configuration (centrally) in AD: security settings, registry-based policy settings, group policy preferences (scripts..).
* If we have access or we have escalated in the machine we can abuse them for various attacks like privesc, backdoors, etc.
* Domain Admins can make many changes across OUs for users/computers/other objects.

### List GPOs

```bash
#PowerView:
Get-NetGPO # List all GPOs
Get-NetGPO -Domain $DOMAIN #List all GPOs form another trusted domain
Get-NetGPO -ComputerName $COMPUTER_NAME # GPO applied to a particular machine
gpresult /R /V # Get policy settings on current machine
```

### Get GPOs which use restricted groups or groups.xml for interesting users

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetGPOGroup #List of the restricted groups (these groups which are pushed though the GPO and are part of a local group on the current machine).
```
{% endcode %}

### Get users which are in a local group of a machine using GPO

```bash
#PowerView:
Find-GPOComputerAdmin -ComputerName $COMPUTER_NAME
```

### Get machines where the given user is member of a specific group

```bash
#PowerView:
Find-GPOLocation -Username $USERNAME -Verbose
```

### Get GPO applied to a OU

```bash
#PowerView:
Get-NetGPO -GPOName "{$GPLINK}" #Get the GPLINK from the Get-NetOU -FullData

#AD Module:
Get-GPO -Guid $GPLINK
```
