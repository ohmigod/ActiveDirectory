---
description: Enumerating AD using BloodHound
---

# BloodHound

> BloodHound provides the capability of mapping shortest path for interesting things like Domain Admins group. It is very noisy, take care.

**BloodHoud have two parts:**

**1. Ingestors:** Collects data for later integration to BloodHound.

**2. GUI:** Where we can upload the data collected (from ingestors).

### Setting up BloodHound

```bash
#1. Execute the binary
cd C:\Tools\BloodHound-win32-x64\BloodHound.exe

#2. Open a cmd and configure neo4j
cd C:\Tools\neo4j-community-3.5.1\bin

#3. Install service and start it
neo4j.bat install service
neo4j.bat start

#4. Now go back to BloodHound and change default password.
```

### Collecting data

{% code overflow="wrap" %}
```bash
#1. Move to ingestors path
cd C:\Tools\BloodHound-master\Ingestors

#2. Load the script in-memory
. .\SharpHound.ps1

#3. Run ingestors
Invoke-BloodHound -CollectionMethod All -Verbose #Run all collection methods, will generate a zip

Invoke-BloodHound -CollectionMethod All -ExcludeDC -Verbose #This one will avoid detections from Advanced Threat Analytics (ATA)
```
{% endcode %}

### Uploading data

Just upload the .zip file generated from ingestors.

**Note:** Sometimes BloodHoud misses the "sessions" part. Manually collect it and upload again:

```bash
Invoke-BloodHound -CollectionMethod LoggedOn -Verbose
Invoke-BloodHound -CollectionMethod GPOLocalGroup -Verbose
```

### BloodHound Queries

There are some pre-built queries, but we can create custom ones. Also, **it is possible to mark users as "owned" to help BloodHound find new paths**. Some pre-built queries are:

* Find all Domain Admins.
* Shortest path to Domain Admins.
* Map Domain trusts.
* ..

We can also generate a map from an object-object (for example: user**XX** to machine**XX**) and BloodHound will try to find the shortest path.
