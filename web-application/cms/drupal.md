---
description: Drupal Vulnerabilities
---

# Drupal

### Identify Drupal Version

Version should be in the following URL:

```bash
http://$IPADRESS/CHANGELOG.TXT
```

### Fuzz Drupal nodes

Fuzz with Burp Suite Intruder (or any other similar tool) on "/node/$" where "$" is a number (from 1 to X00). Chances are that we will find hidden pages (test, dev...) which are not referenced by the search engines.

```
https://target.com/node/1
https://target.com/node/2
…
https://target.com/node/500
```

### Tools

#### [Droopescan](https://github.com/droope/droopescan)

A plugin-based scanner that aids security researchers in identifying issues with several CMS.&#x20;

```bash
droopescan scan drupal -u $URL -t $THREADS
```

#### [Drupwn](https://github.com/immunIT/drupwn)

An efficient way to gather drupal information.

```bash
#Enumeration:
sudo python3 drupwn --mode enum --target $TARGET

#Check possible exploits:
sudo python3 drupwn --mode exploit --target $TARGET
drupwn> list
drupwn> check CVE-XXXX-XXXX
```
