---
description: Disable Windows Defender
---

# Disable Windows Defender

### Disable Windows Defender real time monitoring/protection

Following commands will disable AV real time monitoring/protection. Note that we need local admin privileges to do it.

{% code overflow="wrap" %}
```bash
#Disable all real time monitoring.
Set-MpPreference -DisableRealTimeMonitoring $true

#Disable only the download & execute stuff.
Set-MpPreference -DisableIOAVProtection $true 
```
{% endcode %}

.
