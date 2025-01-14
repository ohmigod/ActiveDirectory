---
description: Find AD computer with LAPS enabled
---

# LAPS

## [LAPSToolkit](https://github.com/leoloobeek/LAPSToolkit)

Functions written in PowerShell that leverage PowerView to audit and attack Active Directory environments that have deployed Microsoft's Local Administrator Password Solution (LAPS). It includes finding groups specifically delegated by sysadmins, finding users with "All Extended Rights" that can view passwords, and viewing all computers with LAPS enabled.

<pre class="language-bash" data-overflow="wrap"><code class="lang-bash">#Load functions in memory:
. .\LAPSToolkit.ps1

#Displays all computers with LAPS enabled, password expriation, and password if user has access:
Get-LAPSComputers

#Searches through all OUs to see which AD groups can read the ms-Mcs-AdmPwd attribute:
Find-LAPSDelegatedGroups

<strong>#Parses through ExtendedRights for each AD computer with LAPS enabled and looks for which group has read access and if any user has "All Extended Rights":
</strong>Find-AdmPwdExtendedRights
</code></pre>
