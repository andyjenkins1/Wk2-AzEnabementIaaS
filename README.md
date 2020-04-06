# Wk2-AzEnablementIaaS

As discussed in week one of our Native Azure Enablement series, everyone in the wider Cloud Incubation and Activation team now has access to an Azure Environment for the purpose of training and education.  To access this environment simply head to portal.azure.com and login with your VMware Credentials.

Week 2 of our Azure Enablement Series is all about Azure IaaS services with a focus on Azure Virtual Machines. The following activities will help you to become familiar with some of the key concepts and how they work.

> In Week 1 we highlighted the key features of the Azure portal and we highly recommend watching the recording to help you to get the most from this session.  Recording and Presentation links available on our Confluence site.

## Activity 1 - What VM Images & Sizes are Available in a Given Region

At this point we are assuming you are logged into the Azure Portal and will either see the homepage as per below or the dashboard.

![AzurePortal](https://ajimagestores.blob.core.windows.net/azenablementwk2/AzurePortal.jpg)

### Step 1 - Using Commands to see what VM Images & Sizes Available

There are wide range of Virtual Machine options within Azure and to make things easier these are grouped into what are known as Families e.g D-Series M-Series etc.  To find out more about the different kinds of VM and what use cases they are best suited, check out <https://azure.microsoft.com/en-gb/pricing/details/virtual-machines/series/>

> Not all VM Types are available in all regions, lets check that out...

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
