# Wk2-AzEnablementIaaS

As discussed in week one of our Native Azure Enablement series, everyone in the wider Cloud Incubation and Activation team now has access to an Azure Environment for the purpose of training and education.  To access this environment simply head to portal.azure.com and login with your VMware Credentials.

Week 2 of our Azure Enablement Series is all about Azure IaaS services with a focus on Azure Virtual Machines. The following activities will help you to become familiar with some of the key concepts and how they work.

> In Week 1 we highlighted the key features of the Azure portal and we highly recommend watching the recording to help you to get the most from this session.  Recording and Presentation links available on our Confluence site.

## Activity 1 - What VM Images & Sizes are Available in a Given Region

At this point we are assuming you are logged into the Azure Portal and will either see the homepage as per below or the dashboard.

![AzurePortal](https://ajimagestores.blob.core.windows.net/azenablementwk2/AzurePortal.jpg)

### Step 1 - Getting Started with Cloud Shell

There are wide range of Virtual Machine options within Azure and to make things easier these are grouped into what are known as Families e.g D-Series M-Series etc.  To find out more about the different kinds of VM and what use cases they are best suited, check out <https://azure.microsoft.com/en-gb/pricing/details/virtual-machines/series/>

> Remember not all VM Types are available in all regions ...

Bring up the Cloud Shell Terminal by clicking on the Terminal Icon at the top of the navigation bar.  Once loaded you will see 

Insert ScreenShot

If you are opening the Cloud Shell for the first time, you will be asked to create a storage account 

Insert ScreenShot

For most users you can just select **Create Storage** but you can also select advanced options which gives you more control on where the storage account is created.  

Insert Screenshot

> The Storage Account is required so that all your data can be persisted and reused in future Cloud Shell sessions.

Once your storage account is created (only required on first run) you will be presented with the command prompt as per the screen below 

Insert ScreenShot

### Step 2 - Making Sure You Are Using the Right Subscription

You may have access to multiple Azure Subscriptions, you can check this with the command 

```bash
az account list
```

This will display a list of all the Azure Subscriptions for which you have access.  To set the account to the AVS Pre-Sales Sub type 

```bash
az account set --subscription 'VMware AVS Pre-Sales Team'
```

### Step 3 - Check out VM Sizes in a Given Region using Azure CLI

```bash
az vm list-sizes -l northeurope -o table
```

This command will list the the VMsizes and Family Types in the region northeurope and will output the result in a table.  You should see something like this 

InsertScreenshot

As you can see there is a huge range of VM types with differing core counts, OS disks, Data disks all depending on the type of VM you wish to use.

You could now run the same command again and change the region from northeurope to uksouth, you would see a different list of VM's and Family Types based on what is available in that region.

### Step 4 - List all Image Publishers Available in North Europe

Ok from the Azure CloudShell Lets switch from Bash to PowerShell 

InsertScreenshot

The terminal will now load into PowerShell.  As the Terminal is using the latest OpenSource versions of both PowerShell and CLI you can still execute Azure CLI commands in the PowerShell Terminal :-) 

> Our Recommendation is to experiment with both Terminals and once you are happy with one stick with that going forward.  The great thing here is that you have choice.

This time we will try a different command.  Lets take a look at all the different Image Publishers in the North Europe Region 

```powershell
Get-AzVMImagePublisher -l northeurope
```
InsertScreenShot

As you can see, lots of Publishers, lets narrow this down a bit 

```powershell
Get-AzVMImagesku -l northeurope -Publisher Canonical -Offer UbuntuServer | Select Skus
```
This will now display all the SKU's offered by Canonical for UbuntuServer and you should see something like 

InsertScreenShot

We can then narrow the images right the way down to a single one if needed, so lets now pick the 19.04 SKU 

```powershell
Get-AzVMImage -l northeurope -Publisher Canonical -Offer UbuntuServer -Sku 19.04
 ```

As you can see even with a single SKU there are still lots of versions!, from the list created above, choose a version and finally we will have navigated to single image :-) 

```powershell
Get-AzVMImage -l northeurope -Publisher Canonical -Offer UbuntuServer -Sku 19.04 -Version 19.04.202001220
```

InsertScreenShot

**Summary:** We have learned (the hard way) that each Azure Region has its own list of available VM Types and Families and that there is a huge range of Image Publishers that also publish content to Azure regions.  We have also demonstrated some effective CLI commands both with Bash and Powershell. We will come back to Images when we deploy into Azure later on in this tutorial.

## Exercise 2 - Virtual Networks (VNET) Introduction

Before we deploy an Virtual Machines, we wanted to spend a bit of time looking at the basic construct of a Virtual Network as this will help those who come from an AWS background and understand the VPC Concept.


[//]: <> (Adams Section.)

## Activity - Deploy VM using Arm Template

In this section we will walk through the process of deploying a simple VM with an ARM Template

### Prerequisites

In order to follow along and complete this task you will need to have the following

1. Install [Visual Studio Code](https://code.visualstudio.com/download)
2. Install the Azure CLI Tool for mac with brew

    ```bash
    brew update && brew install azure-cli
    ```

    or through PowerShell

    ```powershell
    Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'; rm .\AzureCLI.msi
    ```

3. Ensure you have the following extensions installed, [Azure Resource Manager (ARM) Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) we will be using this extension to deploy the VM
