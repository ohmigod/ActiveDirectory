---
description: AD LoggedOn Domain Users Enumeration
---

# LoggedOn Users

### Get actively logged users on a computer

**Note:** This needs local administrator privileges.

```bash
#PowerView:
Get-NetLoggedon -ComputerName $COMPUTER_NAME
```

### Get locally logged users on a computer

**Note:** This needs remote registry on the target, but this is started by default on server OS's.

```bash
#PowerView:
Get-LoggedonLocal -ComputerName $COMPUTER_NAME
```

### Get the last logged user on a computer

**Note:** This needs administrative rights and remote registry on the target.

```bash
#PowerView:
Get-LastLoggedOn -ComputerName $COMPUTER_NAME
```
