---
description: Pentesting SNMP
---

# SNMP (161/udp, 162udp)

> SNMP (Simple Network Management Protocol )is an Internet Standard protocol to allow network administrators to monitor and modify settings in configuration equipments. Devices that typically support SNMP include cable modems, routers, switches, servers, workstations (Linux & Windows), printers, and more.

### SNMP terms

* **OID:** Short for Object IDentifier. Anything in a device that can be monitored with SNMP has an OID. Example of an OID for a temperature monitor: 1.3.1.6.5.1.4.1.3
* **MIB:** Short for Management Information Base. It's a text file that allows us to translate numerical OIDs into word based OIDs. The file is organized like a tree, where branches represent different organizations or network functions. The leaves of the tree correspond to specific variable values that can then be accessed. For example, the OID above is translated like: 1.3.1.6.5.1.4.1.3 -> SYNOLOGY-TEMP-MIB::temperature.0 If we can access the MIB and know how to read and interpret the info, we can then know each and every device on the network. If we can crack the password on SNMP, we may be able to control each networked device. This would allow us to change configurations, take devices offline, etc.

### SNMP Versions

* **1:** Doesn't offer encryption (information and credentials can be easily intercepted over a local network). You don't need to supply a username, you only need to supply a special password called "community string".
* **2 and 2c:** Same as version 1.
* **3:** More secure than versions 1 and 2 (offers encryption). Get's rid of the community string and makes the admins to specify a username+password.

> SNMP protocols have weak authentication schemes and are commonly left configured with default public and private community strings.

> SNMP version 2c is pretty common since many devices or operating systems doesn't support version 3.

### Bruteforce SNMP services

[Onesixtyone](https://github.com/trailofbits/onesixtyone) is a tool that helps us carry a bruteforce attack on the SNMP protocol. By specifying a wordlist of common "community strings" we can attempt to enumerate SNMP services:

```bash
#We can get the "community string" wordlist in the github repo.
#We can remove -i and add only one IP address
#$IPADDRESSLIST can be a list of IPs from X.X.X.1 to 255 in case of C class network.
onesixtyone -c $WORDLIST -i $IPADDRESSLIST
```

### Snmpwalk

> If we know at least the read-only community string (which is in most cases "public"), we can query SMNP values using snmpwalk.

### Enumerate the entire MIB tree:

```bash
#community string is probably "public"
#-v1 specifies the protocol version, in this case version 1
#-t 10 specifies the timeout period
snmpwalk -c $COMMUNITYSTRING -v1 -t 10 $IPADDRESS
```

### Windows Enum:

#### Enumerating Windows Users:

```bash
snmpwalk -c $COMMUNITYSTRING -v1 $IPADDRESS 1.3.6.1.4.1.77.1.2.25
```

#### Enumerating Running Windows Processes:

```bash
snmpwalk -c $COMMUNITYSTRING -v1 $IPADRESS 1.3.6.1.2.1.25.4.2.1.2
```

#### Enumerating Windows Open TCP Ports:

```bash
snmpwalk -c $COMMUNITYSTRING -v1 $IPADDRESS 1.3.6.1.2.1.6.13.1.3
```

#### Enumerating Windows Installed Software:

```bash
snmpwalk -c $COMMUNITYSTRING -v1 $IPADDRESS 1.3.6.1.2.1.25.6.3.1.2
```

### snmp-check

> Like to snmpwalk, snmp-check allows you to enumerate the SNMP devices and places the output in a very human readable friendly format. It could be useful for penetration testing or systems monitoring.

```bash
#Example usage:
snmp-check $IPADDRESS -c $COMMUNITYSTRING
#We can get rid of -c, default community string is "public".
```
