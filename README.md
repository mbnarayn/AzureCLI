# Azure CLI
### Install Azure CLI on Windows
You can install the Azure CLI using PowerShell. Start PowerShell as administrator and run the following command:  
```
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
This will download and install the latest version of the Azure CLI for Windows. If you already have a version installed, it will update the existing version. After the installation is complete, you will need to reopen PowerShell to use the Azure CLI.
### Find your installed version of Azure CLI
```
az --version
```
