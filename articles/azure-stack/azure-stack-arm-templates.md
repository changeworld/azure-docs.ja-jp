---
title: Use Azure Resource Manager templates in Azure Stack (tenant developers) | Microsoft Docs
description: Learn how to use Azure Resource Manager templates in Azure Stack to deploy and provision all of the resources for your application in a single, coordinated operation.
services: azure-stack
documentationcenter: ''
author: heathl17
manager: byronr
editor: ''

ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: helaw

---
# <a name="use-azure-resource-manager-templates-in-azure-stack"></a>Use Azure Resource Manager templates in Azure Stack
Azure Resource Manager templates deploy and provision all of the resources for your application in a single, coordinated operation. You define the resources for the application and how it will be deployed.

These templates can be deployed with the Microsoft Azure Stack portal, PowerShell, the command line, and Visual Studio.

[!VIDEO https://channel9.msdn.com/Blogs/azurestack/Microsoft-Azure-Stack-TP1--Foundational-Skills-1-Deploying-JSON-Templates/player]


The following templates are available on [GitHub](http://aka.ms/azurestackgithub):

## <a name="deploy-sharepoint-(non-high-availability)"></a>Deploy SharePoint (non-high availability)
Use the PowerShell DSC extension to create a SharePoint 2013 farm that includes the following:

* A virtual network
* Three storage accounts
* Two external load balancers
* One VM configured as a domain controller for a new forest with a single domain
* One VM configured as a SQL Server 2014 stand-alone server
* One VM configured as a one machine SharePoint 2013 farm

## <a name="deploy-ad-(non-high-availability)"></a>Deploy AD (non-high availability)
Use the PowerShell DSC extension to create an AD domain controller server that includes the following:

* A virtual network
* One storage account
* One external load balancer
* One VM configured as a domain controller for a new forest with a single domain

## <a name="deploy-ad/sql-(non-high-availability)"></a>Deploy AD/SQL (non-high availability)
Use the PowerShell DSC extension to create a SQL Server 2014 stand-alone server that includes the following:

* A virtual network
* Two storage accounts
* One external load balancer
* One VM configured as a domain controller for a new forest with a single domain
* One VM configured as a SQL Server 2014 stand-alone server

## <a name="vm-dsc-extension-azure-automation-pull-server"></a>VM-DSC-Extension-Azure-Automation-Pull-Server
Use the PowerShell DSC extension to configure an existing virtual machine Local Configuration Manager (LCM) and register it to an Azure Automation Account DSC Pull Server.

## <a name="create-a-virtual-machine-from-a-user-image"></a>Create a virtual machine from a user image
Create a virtual machine from a custom user image. This template also deploys a virtual network (with DNS), public IP address, and a network interface.

## <a name="simple-vm"></a>Simple VM
Deploy a simple Windows VM that includes a virtual network (with DNS), public IP address, and a network interface.

## <a name="cancel-a-running-template-deployment"></a>Cancel a running template deployment
To cancel a running template deployment, use the `Stop-AzureRmResourceGroupDeployment` PowerShell cmdlet.

## <a name="next-steps"></a>Next steps
[Deploy templates with the portal](azure-stack-deploy-template-portal.md)

[Azure Resource Manager overview](../resource-group-overview.md)

<!--HONumber=Oct16_HO2-->


