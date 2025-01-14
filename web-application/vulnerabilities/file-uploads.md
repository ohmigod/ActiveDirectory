---
description: File Uploads
---

# File Uploads

> If a web application has this type of vulnerability, an attacker can upload a file with malicious code in it that can be executed on the server.

### Test application filters

Try to upload a php, aspx, jsp.. with a reverse/web shell and see if the application performs any checks.

### Bypass Content-Type filter

When uploading a file, the browser automatically sets a Content-Type (inside the multipart/form-data) field in the request. If the checks are performed only using the Content-Type, we can bypass it by changing it with Burp.

### Bypass file extension&#x20;

Most file upload apps will typically have some sort of protection in place such as a whitelist / blacklist of allowed / disallowed file extensions. While white lists are the more secure method of protecting file upload apps, you'd be surprised how many times they utilize a blacklist. Try the less used file extensions.&#x20;

Here is a list of alternate extensions listed by language:

* **PHP:** .php, .php2, .php3, .php4, .php5, .php6, .php7, .phps, .phps, .pht, .phtm, .phtml, .pgif, .shtml, .htaccess, .phar, .inc
* **ASP:** .asp, .aspx, .config, .ashx, .asmx, .aspq, .axd, .cshtm, .cshtml, .rem, .soap, .vbhtm, .vbhtml, .asa, .cer, .shtml
* **JSP:** .jsp, .jspx, .jsw, .jsv, .jspf, .wss, .do, .action
* **Coldfusion:** .cfm, .cfml, .cfc, .dbm
* **Flash:** .swf
* **Perl:** .pl, .cgi
* **Erlang Yaws Web Server:** .yaws

### Bypass Backlist filter

Some filters use a blacklist to not allow to upload, for example, .php applications. The system can be vulnerable and we can bypass the filter changing the extensions to:

```bash
.pHP, .PHP, .PHp, PhP..
```

If the file is uploaded but then we can not execute it, we can try to upload and override the .htacces to allow execution. The contents of the new .htaccess would be:

```bash
#Change $extension with .PHp, .PHP..
AddType application/x-httpd-php $extension
```

Also, the blacklist can be bypassed by uploading a language-supported extension which comes enabled by default (these doesn't requiere to add a new .htaccess):

```bash
.php5, .php4, .php3...
```

### Bypass Whitelist filter

Imagine only .gif images are allowed. In apache servers, files can have more than one extension, and the order of the extensions is normally irrelevant (if the mime type is different). If the mime is the same, only the extension at the right will be processed

```bash
.php.gif
```

This vulnerability is patched on new Apache versions, but still vulnerable on old systems. Also there are some times where the file uploaded have an extra check (GetImagesize()). We can bypass this by appending a PHP comment to our .gif:

```bash
gisicle --comment <?php $WEBSHELL_CODE_ONELINER ?> < $file.gif > $file.php.gif
```

To make the webshells to oneliner:

```bash
tr '\n' ' ' < webshell.php
```

### NullByte attack

Generate a file with double extension (.php.gif) and add a webshell inside an image (using [gifsicle](https://github.com/kohler/gifsicle)). Then, grab the request with burp and change the name of the file to file.php(nullbyte).gif. This can be done with burp HEX view.

### XSS on file uploads

If you find a file upload function for an image, try introducing an image with XSS in the filename like:

```bash
#https://twitter.com/Steiner254/status/1564118488175755267
<img src=x onerror=alert('XSS')>.png 
"><img src=x onerror=alert('XSS')>.png 
">.svg <alert('xss')a.png
<svg onload=prompt%26%230000000040document.domain)>
<svg onload=prompt%26%23x000000028;document.domain)>
```
