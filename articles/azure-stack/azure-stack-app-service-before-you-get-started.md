---
title: Before you deploy App Services on Azure Stack | Microsoft Docs
description: Steps to complete before deploying App Service on Azure Stack
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 4/6/2017
ms.author: anwestg
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: e782c051186750afd3f0c6cd2ffc9bf0adbef6aa
ms.lasthandoff: 04/07/2017


---
# <a name="before-you-get-started-with-app-service-on-azure-stack"></a>Before you get started with App Service on Azure Stack

You need a few items to install App Service on Azure Stack:

- A completed deployment of [Azure Stack Technical Preview 3](azure-stack-run-powershell-script.md)
- Enough space in your Azure Stack system for a small deployment of App Service on Azure Stack.  The required space is roughly 20 GB of disk space.
- A Windows Server VM Image for use when creating Virtual Machines for App Service on Azure Stack
- [A server that's running SQL Server](#SQL-Server).

>[!NOTE] 
> The following steps ALL take place on the MAS-CON01 VM.

## <a name="before-you-deploy-app-service-on-azure-stack"></a>Before you deploy App Service on Azure Stack

To deploy a resource provider, you must run the PowerShell Integrated Scripting Environment(ISE) as an administrator. For this reason, you need to allow cookies and JavaScript in the Internet Explorer profile that you use to sign in to Azure Active Directory.

## <a name="turn-off-internet-explorer-enhanced-security"></a>Turn off Internet Explorer enhanced security

1.    Sign in to the Azure Stack proof-of-concept (POC) computer as **AzureStack/administrator**, and then open **Server Manager**.
2.    Turn off **Internet Explorer Enhanced Security Configuration** for both admins and users.
3.    Sign in to the MAS-CON01.AzureStack.local virtual machine as an administrator, and then open **Server Manager**.
4.    Turn off **Internet Explorer Enhanced Security Configuration** for both admins and users.

## <a name="enable-cookies"></a>Enable cookies

1.    Select **Start**, > **All apps**, > **Windows accessories**. Right-click **Internet Explorer** > **More** > **Run as an administrator**.
2.    If you are prompted, select **Use recommended security**, and then select **OK**.
3.    In Internet Explorer, select **Tools** (the gear icon), > **Internet Options** > **Privacy** > **Advanced**.
4.    Select **Advanced**. Make sure that both of the **Accept** check boxes are selected, and then select **OK** and **OK** again.
5.    Close Internet Explorer, and restart PowerShell ISE as an administrator.

## <a name="install-powershell-for-azure-stack"></a>Install PowerShell for Azure Stack

Follow the steps in this article [Install PowerShell](azure-stack-powershell-install.md)

## <a name="using-visual-studio-with-azure-stack"></a>Using Visual Studio with Azure Stack

Follow the steps in this article - [Install Visual Studio](azure-stack-install-visual-studio.md)

## <a name="add-a-windows-server-2016-vm-image-to-azure-stack"></a>Add a Windows Server 2016 VM image to Azure Stack

App Service deploys a number of virtual machines and as such requires a Windows Server 2016 VM Image be available within Azure Stack - [Add a default virtual machine image](azure-stack-add-default-image.md)

## <a name="SQL-Server"></a>SQL Server

By default, the App Service on Azure Stack installer is set to use the SQL Server that is installed along with the Azure Stack SQL Resource Provider. When you install the SQL Server Resource Provider (SQL RP), ensure you take note of the database administrator username and password. You need both when you install App Service on Azure Stack.
>[!NOTE]
> You can deploy and use another server to run SQL Server. You can choose the SQL Server instance to use when you complete the options in the App Service on Azure Stack installer.

## <a name="next-steps"></a>Next steps

- [Install the App Service Resource Provider](azure-stack-app-service-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-before-you-get-started/PSGallery.png
[2]: ./media/azure-stack-app-service-before-you-get-started/WebPI_InstalledProducts.png

