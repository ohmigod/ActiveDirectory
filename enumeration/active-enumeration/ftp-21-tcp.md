---
description: Pentesting FTP
---

# FTP (21/tcp)

> The File Transfer Protocol (FTP) is a standard network protocol used for the transfer of computer files between a client and server on a computer network.

### Banner Grabbing

```bash
telnet -vn $IP $PORT
openssl s_client -connect $IP $PORT-starttls ftp #Get certificate if any
```

### Anonymous login

```bash
anonymous : anonymous
anonymous : 
ftp : ftp
```

### Brute-force

[Here is a nice list with default ftp credentials](https://github.com/danielmiessler/SecLists/blob/master/Passwords/Default-Credentials/ftp-betterdefaultpasslist.txt)

### Download all files from FTP

```bash
wget -m ftp://anonymous:anonymous@10.10.10.98 #Donwload all
wget -m --no-passive ftp://anonymous:anonymous@10.10.10.98 #Download all
```
