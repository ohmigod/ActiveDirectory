---
description: Powershell Remoting
---

# Powershell Remoting

Once we have escalated local privs on the current machine or other machines, recall the enumeration within that new user/machine. Not all the enumeration, just the intrusive ones (LocalAdminAccess, Domain Privs..) to check if we can get anynthing new.

### Powershell remoting

Powershell remoting is an admin capability in new Windows versions to allow sysadmins do multiple things on multiple machines.

If it is disabled, we need to enabled PS remoting (or make a FW exception). It **needs local admin privs by default**:

```bash
Enable-PSRemoting
```

### PSRemoting - One to One

* Interactive
* Runs in a new process
* Stateful (if done with Net-PSSession).

**Note:** PowerShell Remoting needs local admin privs on the target box (although it can be configured to allow non-admin users).

```bash
Enter-PSSession -ComputerName $COMPUTER_NAME #Get an interactive shell
```

In order to make it stateful, create a new PS session:

{% code overflow="wrap" %}
```bash
$sess = New-PSSession -ComputerName $COMPUTER_NAME #Creates a new session with the target machine
$sess #Run this command to verify the session is established
Enter-PSSession -Session $sess #Enter an interactively shell with that session. We can exit that shell and come back later and it will be stateful.
```
{% endcode %}

Or specifying credentials:

{% code overflow="wrap" %}
```bash
$SecPassword = ConvertTo-SecureString 'Test123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('DOMAIN\User', $SecPassword)

$session = New-PSSession -ComputerName x.x.x.x -Credential $Cred
Invoke-Command -Session $session -ScriptBlock {whoami}
```
{% endcode %}

### PSRemoting - One to Many

bypass

* Non interactive
* Executes commands paralely in many machines
* Can run commands and scripts

```bash
Invoke-Command -ComputerName $COMPUTER_NAME -ScriptBlock{whoami;hostname} #Run the commands on the remote machine.

#Other flags
-Credential #To pass user and password. It is optional
-FilePath $PATH_TO_FILE #Executes a script into the target
-ArgumentList #Send arguments to the script
-Session $SESS #Send command or a script to a session (established with New-PSSession)
```

**Note:** Remember to call the function in the end of the script if we are going to pass it through Invoke-Command.

#### Load in-memory functions to another machine

{% code overflow="wrap" %}
```bash
1. Load function localy.
2. See the name of the function with the command: "ls function:"
3. Load the function in the remote machine: Invoke-Command -ComputerName $COMPUTER_NAME -ScriptBlock ${function:functionName}
```
{% endcode %}

### Load scripts on remote machines

```bash
$sess = New-PSSession -ComputerName $COMPUTER_NAME
Invoke-Command -Session $sess -FilePath $PATH_TO_SCRIPT
Enter-PSSesssion -Sesssion $sess
Invoke-blablabla.. #Execute the function interactively from the file imported.
```

### Using plaintext credentials

{% code overflow="wrap" %}
```bash
$creds = Get-Credential #This will show a pop-up to enter the credentials

#Invoke-Command
Invoke-Command - ComputerName $COMPUTER_NAME -ScriptBlock{whoami;ipconfig} -Credential $creds

#Session
$sess = New-PSSession -ComputerName $COMPUTER_NAME -Credential $creds
Enter-PSSession -Session $sess
```
{% endcode %}
