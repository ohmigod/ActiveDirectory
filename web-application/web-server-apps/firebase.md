---
description: Pentesting Firebase
---

# Firebase

> The Firebase Real-time Database is a cloud-hosted database stored as JSON and synchronized in real-time to every connected client.

### Misconfigured Database

An issue can arise in firebase when developers fail to enable authentication. This vulnerability is very similar to every other database misconfiguration: **no authentication.**&#x20;

If the developers forgot to enable authentication, the database will be exposed to the world. You can easily view the database by appending a “/.json” to the url as shown below:

```
vuln-domain.firebaseio.com/.json
```
