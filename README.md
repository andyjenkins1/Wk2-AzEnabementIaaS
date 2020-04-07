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

Before we deploy any Virtual Machines, we wanted to spend a bit of time looking at the basic construct of a Virtual Network as this will help those who come from an AWS background and understand the VPC Concept.

So lets jump straight in and create a Virtual Network, this time we will use the Portal to mix things up a bit.

### Step 1 - Navigate to the vNet Option

From within the Azure Portal, select create a resource from the left hand side of the navigation pane.  You should see something like this 

![Create Resource](https://ajimagestores.blob.core.windows.net/azenablementwk2/Azure%20Portal%20New%20Resource.jpg)

Type **Virtual Network** in the search bar the following will appear 

![Deploy vNet1](https://ajimagestores.blob.core.windows.net/azenablementwk2/Deploy%20VNet1.jpg)

Select **Create** to bring up the vNet Creation Options


![Deploy vNet2](https://ajimagestores.blob.core.windows.net/azenablementwk2/Deploy%20VNet2.jpg)

### Step 2 - Specify Name and Location for the vNet

The first part of creating a vNet is to tell Azure which *Subscription*, *Region* and *Resource Group* you would like to place the vNet.  

In this example make sure the Subscription is set to **VMware AVS Pre-Sales Team**.  Create a new RG of your choice, making sure to use best-practice naming conventions as per link below.  Next give the vNet a Name and select which region you would like to deploy to.  

![Deploy vNet3](https://ajimagestores.blob.core.windows.net/azenablementwk2/Deploy%20vNet3.jpg)

>  Naming Best Practices for Azure Resources Can Be Found at: https://docs.microsoft.com/en-us/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging 

Next Select **IP Addresses**.

### Step 3 - Define Address Spaces and Subnets

When you create a vNet in Azure a default 10.1.0.0/16 address space is provided by default but can be edited during creation.  Every time you then create a new vNet it will provide (where possible) a non-overlapping address space.

For the purposes of this exercise a /24 will be plenty so simply click on the address space and change to a /24.

In Azure you can only deploy resources to a Subnet.  So we need to create a least 1 x Subnet.  To do this, click on on **Add Subnet**.  Give the subnet a name (in this example subnet1) and apply an address space from the /24 to to the subnet.  In this case we have added a /27.

![Deploy vNet4](https://ajimagestores.blob.core.windows.net/azenablementwk2/Deploy%20vNet4.jpg)

>  You will notice that 5 x IP's in your subnet are reserved for Azure Services.  These at the x.x.x.0-x.x.x.3 and the last address of the subnet x.x.x.1-x.x.x.3.  These are reserved by Azure for the Network Address, Azure Default Gateways, Network Broadcast and Azure DNS IP's to the vNet Space.  Further details can be found at https://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-faq

### Step 4 - Define Security Services

When creating a vNet you can also add other Paid Services.  By default DDoS Protection Basic is provided without charge but can be upgraded to standard further details available at https://docs.microsoft.com/en-us/azure/virtual-network/ddos-protection-overview. You can also enable the Azure Stateful Firewall service which is a cloud based security service that has built in HA and unrestricted cloud scale.  

Azure Firewall is a managed, cloud-based network security service that protects your Azure Virtual Network resources. It's a fully stateful firewall as a service with built-in high availability and unrestricted cloud scalability.  More details onm this service can be found at https://docs.microsoft.com/en-us/azure/firewall/overview

For the purposes of our exercise we can just take the defaults.

At this point you can click **Review and Create** to deploy the vNet.

Within a minute you will have fully routable vNet and Subnet created!.

![Deploy vNet5](https://ajimagestores.blob.core.windows.net/azenablementwk2/Deploy%20vNet5.jpg)


>  To find out more about Azure Virtual Networks, check outhttps://docs.microsoft.com/en-us/azure/virtual-network/virtual-networks-faq


## Exercise 3 - Create a Second vNet and Peer the Network

In our previous exercise we created a shiny new vNet :metal:.  In this exercise we will create a second vNet and then we can peer these two networks together to show how we can route traffic between two different networks.

For our second vNet we will use the Azure CLI.

### Step 1 - Launch the Azure Cloud Shell

From the Azure Portal, launch the Azure Cloud Shell and ensure you are in Bash Mode.

### Step 2 - Create the Second vNet

From within the Azure CLI execute the following command 

```bash
az network vnet create \
  --name avs-vnettestpeer-vnet \
  --resource-group AVS-Wk2Education-RG \
  --address-prefixes 10.2.0.0/24 \
  --subnet-name Subnet2 \
  --subnet-prefixes 10.2.0.0/27
```
If your deployment is successful you will seen a stream of JSON.  The key bit of info we are looking for is Provisioning State Succeeded as per the image below 

![vnetdeploy](https://ajimagestores.blob.core.windows.net/azenablementwk2/vnet%20CLI%20Succeed.jpg)

### Step 3 - Let Check vNet in the Portal

Head back into the Azure portal and open the Resource Group AVS-WK2Education-RG.  You should now see 2 x vNets in the RG!.  Well Done!

### Step 4 - Setup the vNet Peer

Click on the first vNet we created **avs-vnttest-vnet** from the Options Menu from this vNet, Select **Peerings**.  You will see the following 

![NewPeering](https://ajimagestores.blob.core.windows.net/azenablementwk2/New%20Peering.jpg)

Select **Add** Peering

This will bring up the Peering Configuration Menu.  As you can see from the menu below there are quite a few options to configure and lots of flexibility for all sorts of routing scenarios!.  However for our example we can just take the defaults as we have highlighted below.  What is key is that we need to create 2 x Peerings one from each vNet.

![PeerConfig](https://ajimagestores.blob.core.windows.net/azenablementwk2/vnetpeerconfig1.jpg)

Once you have updated as per above, click **OK** to allow the peerings to be completed.

You can check if this has worked correctly by checking on each of the vNets and selecting Peerings, you should see that each one has its own Peer pointing to the other.

Congratulations, You have now routed two different networks together!! :metal:

Our next step will be to deploy VM's to these vNets so we can check our theory!.

>  There is a lot more info on Azure vNet peering and requirements along with the requirements to set this up
https://docs.microsoft.com/en-us/azure/virtual-network/virtual-network-peering-overview

## Exercise 4 - Deploy a VM from the Azure Portal

Now that we have our Networks in Place, its time to deploy some VM's!.  We will show this in a couple of ways.  First let deploy a VM from the Azure Portal!.  

### Step 1 - Find an Image to Deploy

From within the Homepage of the Azure Portal, Click **Create Resource**.  This will take us to the Azure Marketplace.

Lets Deploy a Windows Server 2019.  Type Windows Server into the Search box and you should see 

Insert Screenshot

This will then bring up the landing page for Windows Server.  You will see that there are lots of options.  Select **Windows Server 2019** as per below and click **Create**

Insert Screenshot

Once Deployed connect to it.



[//]: <> (Adams Section.)

## Exercise xx - Deploy VM using ARM Template

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
