---
title: Provision a VM in Azure Stack (tenant) | Microsoft Docs
description: As a tenant, learn how to provision a VM in Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: c86646e1-a12e-493f-b396-f17bfacd60c2
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/12/2016
ms.author: erikje
translationtype: Human Translation
ms.sourcegitcommit: 0aee1c31876060777c32c768e0767a39de86cb2a
ms.openlocfilehash: 3fcd07c74a70d39b3fb8a68819fccf88a01bb1ee


---
# <a name="provision-a-virtual-machine"></a>Provision a virtual machine
As an administrator, you can create virtual machines to evaluate resources before offering them in plans.

## <a name="provision-a-virtual-machine"></a>Provision a virtual machine
1. On the Azure Stack POC computer, log in to `https://portal.azurestack.local` as [an admin](azure-stack-connect-azure-stack.md), and then click **New** > **Virtual machines** > **WindowsServer-2012-R2-Datacenter**.  

   ![](media/azure-stack-provision-vm/image01.png)
2. In the **Basics** blade, type a **Name**, **User name**, and **Password**. For **VM disk type**, choose **HDD**. Choose a **Subscription**. Create a **Resource group**, or select an existing one, and then click **OK**.  
3. In the **Choose a size** blade, click **A1 Basic**, and then click **Select**.  
4. In the **Settings** blade, click **Virtual network**. In the **Choose virtual network** blade, click **Create new**. In the **Create virtual network** blade, accept all the defaults, and click **OK**. In the **Settings** blade, click **OK**.

   ![](media/azure-stack-provision-vm/image04.png)
5. In the **Summary** blade, click **OK** to create the virtual machine.  
6. To see your new virtual machine, click **All resources**, then search for the virtual machine and click its name.

    ![](media/azure-stack-provision-vm/image06.png)

## <a name="next-steps"></a>Next steps
[Storage accounts](azure-stack-provision-storage-account.md)



<!--HONumber=Nov16_HO2-->


