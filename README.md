# Install Azure CLI on Windows
You can also install the Azure CLI using PowerShell. Start PowerShell as administrator and run the following command:  
```
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
This will download and install the latest version of the Azure CLI for Windows. If you already have a version installed, it will update the existing version. After the installation is complete, you will need to reopen PowerShell to use the Azure CLI.


```
notepad $PROFILE
```
This will start notepad and open your PowerShell profile. If the file doesn’t exist, Notepad will prompt you to create it.

Add these lines to the profile script:
```
[system.net.webrequest]::defaultwebproxy = new-object system.net.webproxy('http://ProxyHostName:ProxyPortNumber')
[system.net.webrequest]::defaultwebproxy.credentials = [System.Net.CredentialCache]::DefaultNetworkCredentials
[system.net.webrequest]::defaultwebproxy.BypassProxyOnLocal = $true
```
Save, close and restart PowerShell.
# Versions of PowerShell for Managing Office 365
There are two versions of the PowerShell module that you use to connect to Office 365 and administer user accounts, groups, and licenses:
- Azure Active Directory PowerShell for Graph (cmdlets include AzureAD in their name)
- Microsoft Azure Active Directory Module for Windows PowerShell (cmdlets include MSol in their name)
### Install Azure Active Directory PowerShell for Graph
```PowerShell
Install-Module -Name AzureAD
```
