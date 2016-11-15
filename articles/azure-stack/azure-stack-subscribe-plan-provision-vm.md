---
title: Subscribe to an offer and then provision a VM in Azure Stack (tenant) | Microsoft Docs
description: As a tenant, learn how to subscribe to an offer and then provision a VM in Azure Stack.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/26/2016
ms.author: erikje
translationtype: Human Translation
ms.sourcegitcommit: 0aee1c31876060777c32c768e0767a39de86cb2a
ms.openlocfilehash: 27fa6ac45da171d7194d4d1658da5bd984d1f488


---
# <a name="subscribe-to-an-offer"></a>Subscribe to an offer
Now that you've [created an offer](azure-stack-create-offer.md), test that your tenants can create a subscription.

1. On the Azure Stack POC computer, log in to `https://portal.azurestack.local` as [a tenant](azure-stack-connect-azure-stack.md) and click **Get a Subscription**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image01.png)
2. In the **Display Name** field, type a name for your subscription, click **Offer**, click one of the offers in the **Choose an offer** blade, and then click **Create**.

   ![](media/azure-stack-subscribe-plan-provision-vm/image02.png)
3. To view the subscription you created, click **Browse**, click **Subscriptions**, then click your new subscription.  

   ![](media/azure-stack-subscribe-plan-provision-vm/image03.png)

After you subscribe to an offer, refresh the portal to see which services are part of the new subscription.

## <a name="next-steps"></a>Next steps
[Provision a virtual machine](azure-stack-provision-vm.md)



<!--HONumber=Nov16_HO2-->


