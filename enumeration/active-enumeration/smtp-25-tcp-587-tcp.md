---
description: Pentesting SMTP
---

# SMTP (25/tcp, 587/tcp)

> SMTP (Simple Mail Transfer Protocol) is a protocol for sending e-mail messages between servers. Most e-mail systems that send mail over the Internet use SMTP to send messages from one server to another; the messages can then be retrieved with an e-mail client using either POP or IMAP.

### Banner identification

Possible options to obtain the banner if they have been left with the default configuration:

* Using nmap
* Connect to the server using Telnet o netcat (wait a bit)
* Metasploit auxiliar module: /scanner/smtp/smtp\_version

Once we get the banner, we can search for vulnerabilities if the version is not updated.

### NMAP & msf scripts

```bash
--script=smtp-*
auxiliary/scanner/smtp/smtp_enum
auxiliary/scanner/smtp/smtp_relay
auxiliary/scanner/smtp/smtp_version
```

### User enumeration

SMTP have their own set of commands that we can use in order to enumerate possible registered users in the mail server. Those are: VRFY, EXPN and RCPT TO.

<figure><img src="../../.gitbook/assets/vrfyenum.png" alt=""><figcaption><p>User enumeration using VRFY command.</p></figcaption></figure>

We can automate the user enumeration using different tools:

* **Nmap:** NSE have the script "smtp-enum-users.nse" that searches for valid usernames using the EXPN command (note that the EXPN command sometimes is disabled, so this script will report nothing).
* [**SMTP-user-enum**](http://pentestmonkey.net/tools/user-enumeration/smtp-user-enum)**:** Great tool to automate the user enumeration. It allows to specify the command to use (VRFY, EXPN or RCPT).
* [**ISMTP**](https://github.com/crunchsec/ismtp)**:** Tool to automate the process of SMTP User Enumeration, SMTP Spoofing, and/or SMTP relay.

### Mail spoofing

Sometimes an attacker can spoof legitimate e-mail addresses to perform social engineering / phishing attacks. This issue is possible because the SMTP server does not require any authentication to send e-mails:

<figure><img src="../../.gitbook/assets/smtp-spoof.jpeg" alt=""><figcaption><p>SMTP e-mail spoofing. Source <a href="https://luemmelsec.github.io/Pentest-Everything-SMTP/">here</a>.</p></figcaption></figure>

A correct configuration should require authentication, as shown below:

<figure><img src="../../.gitbook/assets/smtp-spoof2.png" alt=""><figcaption><p>Unable to perform e-mail spoofing.</p></figcaption></figure>

**Note:** If you need to connect to a server that only allows encrypted communication, you can use Openssl:

```bash
openssl s_client -starttls smtp -connect <SMTP-server>:587
```
