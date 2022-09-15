---
description: Drupal Vulnerabilities
---

# Drupal

### Identify Drupal Version

Version should be in the following URL:

```bash
http://$IPADRESS/CHANGELOG.TXT
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