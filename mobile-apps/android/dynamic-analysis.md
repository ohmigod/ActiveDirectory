---
description: Android Apps Dynamic Analysis
---

# Dynamic Analysis

###

###

### Bypass SSL Pinning

SSL pinning stands for Secure Socket Layer. SSL certificate creates a foundation of trust by establishing a secure connection. This connection ensures that all data passed between the web server and browsers remain private and integral. If interception with Burp (after installing the certificate on the device) is not working, try some tools, like **TrustMeAlready**, to bypass the pinning.

### Analyze communications

Most often mobile apps talk to an API hosted on a server that might contain security vulnerabilities. Intercept traffic with Burpsuite while using the application and look for them (IDOR, SQLInjection, HTTP (GET/POST) with sensitive data...).&#x20;

### Logfiles

Execute **logcat** and use the application as a normal user (register a new accounts, log in, make profile changes, add a payment card...) Then look at the resulting file to see if sensitive data is stored.&#x20;

```bash
adb connect 192.168.0.25:5555
adb shell
logcat > app.log
```

Also, check the following paths where log data might be stored:

<pre><code><strong>/data/data/&#x3C;app>
</strong>/storage/emulated/0
/mnt/extSdCard
/mnt/external_sd
</code></pre>

### Back-and-Refresh attack

Try to return to an authenticated zone, immediately after having logged out by using the back button.

### Path traversal injection attack

Analyze with [drozer](https://github.com/WithSecureLabs/drozer) if you have any content provider with null read and/or write permissions.

```bash
Drozer# run app.provider.read content://<provider>.<authority>/../../../proc/cpuinfo
```
