---
description: AD Computers Enumeration
---

# Computer Enumeration

### Get a list of computers (DC's, servers...)

**Note:** This returns the objects marked as computers. Doesn't necessarily mean there's a machine behind that object.

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetComputer #List all computer objects
Get-NetComputer -Domain $DOMAIN #List all computers on other domains.
Get-NetComputer -FullData #List all attributes
Get-NetComputer -Ping #Only machines that responds to a ping (could cause false negatives)
Get-NetComputer -OperatingSystem "*Server 2016*" #Change 'Operatingsystem' with the filter type we want (from -FullData)

#AD Module:
Get-ADComputer -Filter * | select Name #List all computer objects
Get-ADComputer -Filter 'OperatingSystem -like "*Server 2016*"' -Properties OperatingSystem | select Name,OperatingSystem
Get-ADComputer -Filter * -Properties DNSHostName | %{Test-Connection -Count 1 -ComputerName $_.DNSHostName}
Get-ADComputer -Filter * -Properties *
```
{% endcode %}

### Unconstrained Delegation

```bash
#PowerView:
Get-NetComputer -Unconstrained
```

### Constrained Delegation

{% code overflow="wrap" %}
```bash
#PowerView_dev:
Get-DomainComputer -TrustedToAuth
```
{% endcode %}
