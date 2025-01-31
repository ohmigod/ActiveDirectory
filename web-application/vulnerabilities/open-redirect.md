---
description: Open redirect
---

# Open Redirect

> A web application accepts a user-controlled input that specifies a link to an external site, and uses that link in a Redirect. This simplifies phishing attacks.

### Basic

When a URL redirect is passed as argument, try changing it:

```bash
https://somesite.com/redirect.php?url=$CHANGE_THIS
```

### Hash Based

Sometimes, an additional parameter is used to check the hash of the URL, and both should be correct:

```bash
https://somesite.com/redirect.php?url=$SOME_URL&hash=HASH_OF_SOME_URL
```

In that case, generate the hash for the new URL.

### Hash Based + Salt

Apart from hash based URLs, there are cases that the hash is salted (MAC):

```bash
salt=123;
encodedurl = sha1(salt+$URL)
```

The salt may be passed as an additional parameter. If that is the case, generate the hash for the new URL. Else, try performing a [hash length extension attack.](cryptography/hash-length-extension.md)
