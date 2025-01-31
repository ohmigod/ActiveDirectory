---
description: Pentesting POP3
---

# POP3 (110/tcp, 995/tcp)

### Banner Grab

```bash
nc -nvC $IPADDRESS 110
```

### Mail enumeration

Found valid mail and password? Log in and view e-mails. This might contain useful information.

```bash
USER $user
+OK
PASS $pass
+OK Welcome
LIST # <- List mails
RETR 1 # <- Read mail number # from the LIST
```

### Authentication attempt

POP3 requires us to log on to the server with the USER command, and then authenticate with the PASS command.

```bash
USER root
+OK
PASS alphabeta
-ERR Authentication failed.

#Try a non existent user to check if the server confirms (+OK) valid usernames.
```

***
