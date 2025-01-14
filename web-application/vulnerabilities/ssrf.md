---
description: Server-Side Request Forgery
---

# SSRF

> Server-side request forgery is a web security vulnerability that allows an attacker to **induce the server-side application to make HTTP requests to an arbitrary domain** of the attacker's choosing.

### Where to find SSRFs

* Any part of an http request that includes URLs (including headers)&#x20;
* API specification imports (WSDL imports)&#x20;
* Other file imports&#x20;
* Connection to remote servers (e.g. FTP)&#x20;
* "ping" or "alive" checks &#x20;

<mark style="color:red;">**!**</mark> When discovering an SSRF, inspect the full request being made by the server (including headers) since it might contain sensitive header/body information.

### Useful thing to consider

SSRFs are not limited to the HTTP protocol. Try using another one:

```
http://xxx.xxx
file:///etc/passwd
php://xxx.xxx
```

### Bypasses

There are some ways to bypass filters/WAFs:

```bash
#Random Case
http://LoCaLhOsT:8080
hTtP://localHOST:8080

#Change encoding
http://127.0.0.0.1 #Decimal
http://0x7f000001 #Hex
http://0177.0.0.01 #Octal
http://127.1 #Zero compression
http://127.0.00000000000000000000000000000000001 #Zero compression
http://2130706433 #DWORD
http://01111111000000000000000000000001 #Binary
```

### Blind SSRF

Blind SSRF vulnerabilities occur when an application is making a request to a back-end server due to some reasons but the response is not shown on the front-end.

### Collaborator Everywhere

Collaborator Everywhere (requires burp pro) is a great extension to find normal/blind SSRFs by injecting non-invasive headers into proxy requests and designed to reveal backend systems by causing pingbacks to Burp Collaborator. [Read more](https://portswigger.net/research/cracking-the-lens-targeting-https-hidden-attack-surface).

### Playing with redirections

Imagine that localhost is filtered but the application follows redirects. We can perform a SSRF to  our controlled server with a redirecting to localhost:

```bash
#Start a netcat listener
nc -nlvp 80 < REQ 

#Content of REQ file 
HTTP/1.1 301 MOVED PERMANENTLY 
Location: http://localhost 
```

### SSRF to XSS

If the application renders the content of a file, we can add a script to a file and perform an XSS.

```html
<html>
<head></head>
<body>
<script>alert(document.cookie)</script>
</body>
</html>
```

### Enumerating local services

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
