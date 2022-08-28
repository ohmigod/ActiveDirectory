---
description: 403 Bypasses
---

# 403 Bypass

### Bypass scripts

Try different scripts which bypass 403.&#x20;

{% embed url="https://github.com/iamj0ker/bypass-403" %}
bypass-403
{% endembed %}

{% embed url="https://github.com/Dheerajmadhukar/4-ZERO-3" %}
4-ZERO-3
{% endembed %}

{% embed url="https://github.com/PortSwigger/403-bypasser" %}
403-bypass (Burpsuite)
{% endembed %}

### Directory brute-forcing

Following is an example of this method.

```
https://target.site/admin/ #403 Forbidden

https://target.site/admin/users.php #200 OK
https://target.site/admin/adduser.php #200 OK
```

### Archive history

Search for endpoints on [Waybackmachine](https://archive.org/web/) or any other history database.