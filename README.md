# Azure CLI
### Install or Update Azure CLI on Windows
You can install the Azure CLI using PowerShell. Start PowerShell as administrator and run the following command:  
```
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
This will download and install the latest version of the Azure CLI for Windows. If you already have a version installed, it will update the existing version. After the installation is complete, you will need to reopen PowerShell to use the Azure CLI.
### Find your installed version of Azure CLI
```
az --version
```
### Sign in with Azure CLI
```
az login
```
### Get subscriptions that the current account can access
```
az account list
```
On the the Cloud Shell this command only lists the subscriptions for the directory that is currently selected in the Poral
### Show currently selected Azure subscription
```
az account show
```
### Change subscription context
```
az account set --subscription "Company Subscription"
```
### List all resources in a subscription
```
az resource list
```
### Create a Resource Group
```
az group create -l uksouth -n MyResourceGroup
```
