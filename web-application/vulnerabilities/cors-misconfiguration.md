---
description: Cross-Origin Resource Sharing Vulnerabilities
---

# CORS Misconfiguration

> Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources. It has been introduced in HTML5 to soften the Same Origin Policy.

### Bypass Origin Filter

```bash
#Allowed domain
Origin: evil.com 

#Bypasses
Origin: target.evil.com 
Origin: target.com.evil.com 
```
