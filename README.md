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
On the Cloud Shell this command only lists the subscriptions for the directory that is currently selected in the Portal
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
### List all Resource Groups in a Subscription
```
az group list -o table
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
Note: If you run the az network nsg rule create command and specify a NSG rule name that is already in use for either an Inbound or an Outbound rule, the existing NSG rule is replaced with the parameters of the new rule without any warning.

### Delete NSG Rules
```
az network nsg rule delete --resource-group $rgname --nsg-name $subnetname1 --name AllowAllIntraSubnetTraffic
```
### Update NSG Rules - Examples
```
az network nsg rule update --name AllowRdpFromAnywhere --nsg-name $subnetname1 --resource-group $rgname --source-address-prefixes 195.12.20.20/32
az network nsg rule update --name AllowRdpFromAnywhere --nsg-name $subnetname1 --resource-group $rgname --source-port-range "3389" --destination-address-prefix "10.50.1.1/32"
```
### Get a list of popular VM images in the Azure VM Marketplace (Table Format)
```
az vm image list --output table
```
### Get a list of all VM images in a particular Azure region (Table Format)
```
az vm image list -l uksouth --all --output table
```
### Get a list of all VMs in the subscription (Table Format)
```
az vm list -o table
```
### Delete a Virtual Machine
```
az vm delete --resource-group rgname --name vmname
```
### Get a list of all Managed Disks in the subscription (Table Format)
You can use the --query parameter to customize the properties and columns you want to show in the list output. The following example shows how to select just the Managed Disk Name, Disk State and the Resource Group Name in the list command.
```
az disk list --query "[].{resource:resourceGroup, name:name, diskstate:diskState}" -o table
```
### Delete a Resource Group
```
az group delete -n resourcegroupname
```
### List VM sizes in a region
```
az vm list-sizes -l ukwest
```
## Create Resource Group, VNET, Subnet, NSG Rules and Virtual Machine
```
$rgname="edbmigration"
$location="ukwest"
$vnetname="edbmigration"
$vnetaddress="10.205.0.0/16"
$subnetname1="subnet1"
$subnetadd1="10.205.1.0/24"
$vmname1="migration04"
$vm1size="Standard_DS2_v2"
$vmimage1="Win2019Datacenter"
$vmname1ip="10.205.1.11"
$adminusername="vmadmin"
$adminpassword="SecurePassword"
$vmname1datadisk1size="1024"


az group create -l $location -n $rgname

az network vnet create --name $vnetname --resource-group $rgname --location $location --address-prefix $vnetaddress --subnet-name $subnetname1 --subnet-prefix $subnetadd1

az network nsg create --resource-group $rgname --name $subnetname1 --location $location

az network vnet subnet update --vnet-name $vnetname --name $subnetname1 --resource-group $rgname --network-security-group $subnetname1

az network nsg rule create --resource-group $rgname --nsg-name $subnetname1 --name AllowAllIntraSubnetTraffic --access Allow --protocol Tcp --direction Inbound --priority 100 --source-address-prefix "$subnetadd1" --source-port-range "*" --destination-address-prefix "$subnetadd1" --destination-port-range "*"

az network nsg rule create --resource-group $rgname --nsg-name $subnetname1 --name AllowRdpFromLocation --access Allow --protocol Tcp --direction Inbound --priority 101 --source-address-prefix 8.8.8.8 8.8.4.4 1.1.1.1 --source-port-range "*" --destination-address-prefix "VirtualNetwork" --destination-port-range "3389"

az network nsg rule create --resource-group $rgname --nsg-name $subnetname1 --name DenyAll --access Deny --protocol * --direction Inbound --priority 4000 --source-address-prefix "*" --source-port-range "*" --destination-address-prefix "*" --destination-port-range "*"

az vm create --resource-group $rgname --name $vmname1 --image $vmimage1 --admin-username $adminusername --admin-password $adminpassword --public-ip-address-dns-name $vmname1 --data-disk-sizes-gb $vmname1datadisk1size --subnet $subnetname1 --vnet-name $vnetname --private-ip-address $vmname1ip --size $vm1size --storage-sku Premium_LRS --public-ip-address-allocation static --os-disk-name $vmname1 --% --nsg ""
```
To create a VM with no NSG assigned directly to the NIC use --nic "". The --% in the command above stops parsing input as PowerShell commands or expressions. This is only needed when running Azure CLI commands from PowerShell as Powershell will remove "" so you will see error: argument --nsg: expected one argument

Allowed values for --storage-sku: Standard_LRS, Premium_LRS, StandardSSD_LRS, UltraSSD_LRS. Both and OS and Data disk will use the same Storage SKU.

## Create an Availability Set

`az vm availability-set create -n youravsetname -g yourresourcegroup --platform-fault-domain-count 2 --platform-update-domain-count 5`

## Create Virtual Machine on an existing Subnet and Availability Set without a Public IP address

```
$rgname="yourresourcegroup"
$vnetname="yourvnet"
$subnetname1="yoursubnet"
$vmname1="vmname"
$adminusername="vmadmin"
$adminpassword="SecurePassword"
$vmimage1="Win2019Datacenter"
$avset="youravset"
$vm1size="Standard_D4s_v3"

az vm create --resource-group $rgname --name $vmname1 --image $vmimage1 --admin-username $adminusername --admin-password $adminpassword --subnet $subnetname1 --vnet-name $vnetname --size $vm1size --storage-sku StandardSSD_LRS --os-disk-name $vmname1 --availability-set $avset --% --nsg "" --public-ip-address ""
```

## Get details of a VNET including Subnet IDs
```
az network vnet subnet show -g MyResourceGroup -n MySubnet --vnet-name MyVNet
```
