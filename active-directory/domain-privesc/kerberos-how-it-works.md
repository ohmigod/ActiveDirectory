---
description: Kerberos - How it works
---

# Kerberos - How it works

> Kerberos is the defacto authentication mechanism on AD environments.

### How it works

Suppose we want to access an application server from a domain joined client machine. This is what happends:

1. Client sends a timestamp encrypted with the NTLM hash of the user **(AS-REQ)**.
2. KDC responds with a TGT encrypted with the NTLM hash of the krbtgt user **(AS-REP)**.
3. Client sends the TGT back to to the KDC and requests a TGS for a service **(TGS-REQ)**.
4. KDC responds with a TGS encrypted using the service NTLM hash **(TGS-REP)**.
5. Client connects to the app server and presents the TGS to the service **(AP-REQ)**.
6. Optional mutual authentication **(AP-REP)**.

![kerberos-work](https://software.intel.com/sites/manageability/AMT\_Implementation\_and\_Reference\_Guide/ImagesExt/image861\_0.png)

**Note:** Almost every step is abusable!
