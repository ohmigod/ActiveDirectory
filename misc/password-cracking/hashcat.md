---
description: Hashcat
---

# Hashcat

### Identifying hash mode

Look in the [examples](https://hashcat.net/wiki/doku.php?id=example\_hashes) page to see hash formats and its corresponding mode.

### NTLM (1000)

Brute-forcing NTLM with all possible combinations (lower, upper, digits, symbols) up to 8 characters:

```bash
hashcat -m 1000 –a 3 –w 3 -1 ?l?u?d?s hashes.txt ?a?a?a?a?a?a?a?a --increment 
```

### mscache2 (2100)

Brute-forcing mscache2 using the company name as a prefix:

```bash
hashcat -m 2100 –a 3 –1 ?l?u?d?s hashes.txt Company?a?a?a?a --increment 
```

### JWT (16500)

Brute-forcing weak signing key:

```bash
hashcat jwt.txt -m 16500 -a 3 -w 2 ?d?d?d?d #brute-force 
hashcat jwt.txt -m 16500 wordlist.txt #wordlist 
```
