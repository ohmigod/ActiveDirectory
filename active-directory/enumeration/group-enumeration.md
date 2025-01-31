---
description: AD Groups Enumeration
---

# Group Enumeration

### Get all AD groups

```bash
#PowerView:
Get-NetGroup #All groups of the current domain
Get-NetGroup -Domain $DOMAIN #All groups of other domains
Get-NetGroup -FullData #All attributes of groups

#AD Module:
Get-ADGroup -Filter * | select name
Get-ADGroup -Filter * -Properties *
```

### Info of a particular group

```bash
#PowerView:
Get-NetGroup 'Domain Admins' -FullData #Full data (including memberships) of a group
```

### Get all groups containing a specific string

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetGroup "*admin*" #Search for groups containing the word "admin"

#AD Module:
Get-ADGroup -Filter 'Name -like "*admin*"' | select Name #Search for groups containing the word "admin"
```
{% endcode %}

### Get all the members of a group

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetGroupMember -GroupName "Domain Admins" #List all members of the domain admins group (can contain groups)
Get-NetGroupMember -GroupName "Domain Admins" -Recurse #Recurse travel across groups inside the domain admins group and list all users inside.

#AD Module:
Get-ADGroupMember -Identity "Domain Admins" -Recursive
```
{% endcode %}

### Get the group membership of a user

```bash
#PowerView:
Get-NetGroup -UserName "student1"

#AD Module:
Get-ADPrincipalGroupMembership -Identity student1
```

### List all the local groups on a machine

**Note:** This needs administrator privileges on non-dc machines.

{% code overflow="wrap" %}
```bash
#PowerView:
Get-NetLocalGroup -ComputerName $COMPUTER_NAME -ListGroups ##List all local groups
Get-NetLocalGroup -ComputerName $COMPUTER_NAME #List memberships of the built-in local administrators group
```
{% endcode %}

### Get members of all the local groups on a machine

**Note:** This needs administrator privileges on non-dc machines.

```bash
#PowerView:
Get-NetLocalGroup -ComputerName $COMPUTER_NAME -Recurse
```
