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
### Create a VNET
```
az network vnet create --name VNet-Demo --resource-group MyResourceGroup --location uksouth --address-prefixes 192.168.0.0/16 --subnet-name Subnet-01 --subnet-prefix 192.168.1.0/24
```
## Create a VNET with three subnets and associate a NSG to each subnet
#### Define Variables
You can run the Azure CLI with the az command from either Windows Command Prompt or PowerShell, however declaring variables in this format only works when using Azure CLI via PowerShell.
```
$rgname="azcli"
$location="uksouth"
$vnetname="azclivnet2"
$vnetaddress="10.60.0.0/16"
$subnetname1="frontend"
$subnetadd1="10.60.1.0/24"
$subnetname2="middletier"
$subnetadd2="10.60.2.0/24"
$subnetname3="backend"
$subnetadd3="10.60.3.0/24"
```
#### Create a VNET with three Subnets
```
az network vnet create --name $vnetname --resource-group $rgname --location $location --address-prefix $vnetaddress --subnet-name $subnetname1 --subnet-prefix $subnetadd1
az network vnet subnet create --address-prefix $subnetadd2 --name $subnetname2 --resource-group $rgname --vnet-name $vnetname
az network vnet subnet create --address-prefix $subnetadd3 --name $subnetname3 --resource-group $rgname --vnet-name $vnetname
```
#### Create three NSGs and associate a NSG to each Subnet
```
az network nsg create --resource-group $rgname --name $subnetname1 --location $location
az network vnet subnet update --vnet-name $vnetname --name $subnetname1 --resource-group $rgname --network-security-group $subnetname1
az network nsg create --resource-group $rgname --name $subnetname2 --location $location
az network vnet subnet update --vnet-name $vnetname --name $subnetname2 --resource-group $rgname --network-security-group $subnetname2
az network nsg create --resource-group $rgname --name $subnetname3 --location $location
az network vnet subnet update --vnet-name $vnetname --name $subnetname3 --resource-group $rgname --network-security-group $subnetname3
```
### Create NSG Rules
```
az network nsg rule create --resource-group $rgname --nsg-name $subnetname1 --name AllowRdpFromAnywhere --access Allow --protocol Tcp --direction Inbound --priority 101 --source-address-prefix "*" --source-port-range "*" --destination-address-prefix "VirtualNetwork" --destination-port-range "*"
az network nsg rule create --resource-group $rgname --nsg-name $subnetname1 --name AllowAllIntraSubnetTraffic --access Allow --protocol Tcp --direction Inbound --priority 100 --source-address-prefix "$subnetadd1" --source-port-range "*" --destination-address-prefix "$subnetadd1" --destination-port-range "*"
```
### Delete NSG Rules
```
az network nsg rule delete --resource-group $rgname --nsg-name $subnetname1 --name AllowAllIntraSubnetTraffic
```
### Update NSG Rules - Examples
```
az network nsg rule update --name AllowRdpFromAnywhere --nsg-name $subnetname1 --resource-group $rgname --source-address-prefixes 195.12.20.20/32
az network nsg rule update --name AllowRdpFromAnywhere --nsg-name $subnetname1 --resource-group $rgname --source-port-range "3389" --destination-address-prefix "10.50.1.1/32"
```
