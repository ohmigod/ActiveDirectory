# Tools

### Subdomain Enumeration Tools

#### amass (some might include active scans)

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash">#https://github.com/OWASP/Amass
<strong>
</strong><strong>#Reverse whois
</strong><strong>#This method is invoked using the -whois flag. Essentially it takes the details from the specified domain’s whois records, and then tries to find other domains with similar whois records.
</strong>$ amass intel -d target.com -whois

#SSL Certificate Grabbing
#If you feed IP addresses to Amass and give it the -active flag, it pulls the SSL certificate from every IP address within the IP range and then spits back the domain that the SSL cert is associated with
$ amass intel -active -cidr X.X.X.X/X

#Find ASNs
#If you want to find the ASN of a company, amass can do a nice convenient search. 
<strong>$ amass intel -org "Target"
</strong><strong>
</strong><strong>#Find subdomains by ASNs
</strong><strong>$ amass intel -active -asn XXXXX
</strong><strong>
</strong><strong>#All amass intel techniques recursively
</strong><strong>$ amass intel -asn XXXXX -whois -d target.com
</strong><strong>
</strong><strong>#Get some subdomains
</strong><strong>$ amass enum -d target.com
</strong>
#Get more subdomains
$ amass enum -d target.com -active -cidr X.X.X.X/X,X.X.X.X/X -asn XXXXX</code></pre>

#### cert4recon.py

<pre class="language-bash"><code class="lang-bash">#https://github.com/mathis2001/Cert4Recon
<strong>./cert4recon.py -t target.com -a</strong></code></pre>

#### subfinder

```
#https://github.com/projectdiscovery/subfinder
subfinder -d target.com -all
```
