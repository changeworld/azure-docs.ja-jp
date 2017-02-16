---
title: Deploy templates with Visual Studio in Azure Stack | Microsoft Docs
description: Learn how to deploy templates with Visual Studio in Azure Stack.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/12/2017
ms.author: helaw
translationtype: Human Translation
ms.sourcegitcommit: 0c9df7f42efbcab7f53cea3779f359b4c131e926
ms.openlocfilehash: bb9a89aa43deb829575223dfb963d71ef569ae24


---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Deploy templates in Azure Stack using Visual Studio
Use [Visual Studio](azure-stack-install-visual-studio.md) to deploy Azure Resource Manager templates to the Azure Stack POC.

1. Open Visual Studio 2015 Update 1.
2. Click **File**, click **New**, and in the **New Project** dialog box click **Azure Resource Group**.
3. Enter a **Name** for the new project, and then click **OK**.
4. In the **Select Azure Template** dialog box, change the *Show templates from this location* drop-down to **Azure Stack Quickstart**
5. Click **101-Simple-Windows-VM**, and then click **OK**.  
6. In your new project, you can see a list of the templates available by expanding the **Templates** node in the **Solution Explorer** pane.
7. In the **Solution Explorer** pane, right-click the name of your project, click **Deploy**, then click **New Deployment**.
8. In the **Deploy to Resource Group** dialog box, in the **Subscription** drop-down, select your Microsoft Azure Stack subscription.
9. In the **Resource Group** list, choose an existing resource group or create a new one.
10. In the **Resource group location** list, choose a location, and then click **Deploy**.
11. In the **Edit Parameters** dialog box, enter values for the parameters (which vary by template), and then click **Save**.

## <a name="next-steps"></a>Next steps
[Deploy templates with the command line](azure-stack-deploy-template-command-line.md)
[Develop templates for Azure Stack](azure-stack-develop-templates.md)




<!--HONumber=Jan17_HO2-->


