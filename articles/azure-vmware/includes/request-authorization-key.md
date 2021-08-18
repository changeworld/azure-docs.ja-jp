---
title: ExpressRoute の承認キーを要求する
description: ExpressRoute の承認キーを要求する手順。
ms.topic: include
ms.date: 03/15/2021
author: shortpatti
ms.author: v-patsho
ms.service: azure-vmware
ms.openlocfilehash: 5f4c436a8559a4b579a3a0c9bbfbe79bcb50764e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729084"
---
<!-- used in tutorial-expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Azure portal で、Azure VMware Solution のプライベート クラウドに移動します。 **[管理]**  >  **[接続]**  >  **[ExpressRoute]** の順に選択し、 **[+ Request an authorization key]\(+ 承認キーを要求する\)** を選択します。

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute の承認キーを要求する方法を示すスクリーンショット。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. これに名前を指定し、 **[作成]** を選択します。

   キーの作成には約 30 秒かかることがあります。 作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Reach の承認キーを示すスクリーンショット。" lightbox="../media/expressroute-global-reach/show-global-reach-auth-key.png":::
  
1. 承認キーと ExpressRoute ID をコピーします。 これらは、ピアリングを行う際に必要になります。 承認キーはしばらくすると消えるため、表示されたらすぐにコピーしてください。

