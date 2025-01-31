---
description: Pentesting IIS (Internet Information Services)
---

# IIS

> Internet Information Services is an extensible web server software created by Microsoft for use with the Windows NT family.

### Checklist

* [ ] Discover hidden paths/files using [IIS Shortname Scanner](iis.md#iis-shortname) (if vulnerable).
* [ ] Look for usage and vulnerabilities of Telerik UI (not just in the root folder!).&#x20;
* [ ] Check for known MachineKeys using [blacklist3r](https://github.com/NotSoSecure/Blacklist3r).&#x20;
* [ ] Heavy focus on file read vulnerabilities and look for web.config (machineKey = RCE).&#x20;
* [ ] Try uploading a Web.config on file uploads (RCE).&#x20;
* [ ] Look for verbose "padding" errors. Might lead to Padding Oracle Attack.

### Leaking the web.config

If we can leak the web.config file for a .NET application you are testing (via [LFD](../vulnerabilities/directory-traversal.md), [XXE](../vulnerabilities/xxe.md), etc), we can most likely achieve remote code execution by exploiting the VIEWSTATE parameter.

#### Uploading a .shtml file

We might get the contents of the "web.config" if we abuse a file uploader to upload a ".shtml" file with the following contents:

{% code title="file.shtml" overflow="wrap" %}
```
<!--#include file="..\..\web.config" -->
```
{% endcode %}

### Leveraging ViewState

* [ ] Look for MAC validation on Burpsuite using [ViewState Editor](https://portswigger.net/bappstore/ba17d9fb487448b48368c22cb70048dc).&#x20;
* [ ] Heavy focus on file read vulns (MachineKey = RCE).&#x20;
* [ ] Try guessing the MachineKey using [Blacklist3r](https://github.com/NotSoSecure/Blacklist3r/).&#x20;
* [ ] Look for file uploads (web.config = RCE).

### Retrieving MachineKey using blacklist3r

We can brute-force it with a list of keys using [blacklist3r](https://github.com/NotSoSecure/Blacklist3r) (might lead to [RCE](../vulnerabilities/insecure-deserialization.md#.net-deserialization) if found):

{% code overflow="wrap" %}
```
C:\ > AspDotNetWrapper.exe --keypath MachineKeys.txt --encrypteddata /wEPDWUkltkY... --purpose=viewstate --valalgo=sha1 --decalgo=aes --modifier=ca0b0334 --macdecode --legacy
```
{% endcode %}

* **--encrypteddata:** \_\_VIEWSTATE parameter value.
* **--modifier:** \_\_VIEWSTATEGENERATOR parameter value.

### IIS ShortName

> Microsoft IIS contains a flaw that may lead to an unauthorized information disclosure. The issue is triggered during the parsing of a request that contains a tilde character (\~). This may allow a remote attacker to gain access to file and folder name information.

It is possible to detect short names of files and directories which have an 8.3 equivalent in Windows by using some vectors in several versions of Microsoft IIS. For instance, it is possible to detect all short-names of ".aspx" files as they have 4 letters in their extensions.

**IIS ShortName Scanner**

This tool works perfectly to exploit this vulnerability. Syntax:

```powershell
java -jar 2 20 https://target.com/
```
