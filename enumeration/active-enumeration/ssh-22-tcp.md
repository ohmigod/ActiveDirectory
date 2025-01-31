---
description: Pentesting SSH
---

# SSH (22/tcp)

### Manual banner grab + additional info

#### Grab banner:

```bash
nc -nv $IPADDRESS 22
```

#### Additional info:

Try connecting to the server to see if there is a welcome message before password prompt:

```bash
ssh root@$IPADDRESS
#<Banner should be here>
```

### Nmap scripts:

```bash
--script ssh*
```

### See if users are allowed to log in

Once connected to a machine with low-priv shell, use the following command to see if the users can log into ssh (with any password discovered on information gathering):

```bash
grep -v '^#' /etc/ssh/sshd_config | uniq
```

### Connect using specific key exchange method

```bash
ssh -oKexAlgorithms=+diffie-hellman-group-exchange-sha1  $user@$server
```
