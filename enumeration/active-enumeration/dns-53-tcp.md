---
description: Pentesting DNS
---

# DNS (53/tcp)

> During the Information Gathering phase, DNS enumeration is one of the most critical steps. When we mention DNS enumeration, we are referenced to all the techniques we use to gather as much information as possible by querying the DNS server of a website or host.

## Version Identification

```bash
#Using nslookup:
nslookup -q=txt -class=CHAOS version.bind $IPADDRESS

Using dig:
dig version.bind CHAOS TXT @$IPADDRESS
```

## Performing DNS Queries

#### Nslookup:

```bash
#Passive mode:
nslookup -query=$RECORDTYPE
$DOMAIN
#Interactive mode:
nslookup
set q=$RECORDTYPE
$DOMAIN
```

#### Host

```bash
host -t $RECORDTYPE $DOMAIN
```

#### Dig:

```bash
dig  $DOMAIN $RECORDTYPE +noall +answer +nocmd
```

## Zone Transfer

> One of the most common DNS attacks occurred by misconfigured DNS servers to get all the records in order to get an idea of the domain infrastructure. It is unlucky to see this bug nowadays, but it is worth to mention it.

```bash
#Query this with all the name servers for the domain ("NS" record type), maybe a single server has zone transfer enabled.
#Zone transfer with dig command:
dig axfr @$NS $DOMAIN
#Zone transfer with host command:
host -l $DOMAIN $NS
```

[More info about Zone Transfer and all the information you can obtain](https://digi.ninja/projects/zonetransferme.php)

## Others & Tools

#### Forward Lookup Brute Force (one-liner):

```bash
#Change $WORDLIST with a DNS wordlist:
for ip in $(cat $WORDLIST); do host $ip.$DOMAIN.com; done
```

#### Reverse Lookup Brute Force (one-liner):

```bash
#Can add a pipe + grep to filter the output
for ip in $(seq 1 255); do host X.X.X.$ip; done
```

#### [dnsenum](https://github.com/fwaeytens/dnsenum): Auto DNS enum tool.

DNSenum is a tool that it was designed with the purpose of enumerating DNS information about a domain.

```bash
dnsenum $DOMAIN
```

#### [DNSRecon](https://github.com/darkoperator/dnsrecon):

DNSRecon is a Python script that can enumerate general DNS records, perform zone transfers, perform reverse lookups, and brute-force subdomains among other functions.

```bash
dnsrecon -d $DOMAIN -D $WORDLIST -t $TYPEOFSCAN
```
