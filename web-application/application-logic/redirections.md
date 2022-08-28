---
description: Redirection Vulnerabilities
---

# Redirections

### Redirection bypass

If noticed a weird behaviour like "/admin/" redirecting to "/", always investigate these:

```
/admin/ 
/admin/../admin 
//admin/ 
/Admin/ 
/admin;/ 
/Admin;/ 
/index.php/admin/ 
/admin/js/*.js 
/admin/*brute*.ext 
/admin../admin 
//anything/admin/ 
```