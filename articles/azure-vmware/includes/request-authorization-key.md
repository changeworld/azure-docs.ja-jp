---
title: ExpressRoute の承認キーを要求する
description: ExpressRoute の承認キーを要求する手順。
ms.topic: include
ms.date: 03/15/2021
ms.openlocfilehash: 99d9fba33d64fca1d9c5b960041fbabe1f9060db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105026975"
---
<!-- used in expressroute-global-reach-private-cloud.md and create-ipsec-tunnel.md -->

1. Azure portal の **[接続性]** セクションの下にある **[ExpressRoute]** タブで **[+ Request an authorization key]\(承認キーの要求\)** を選択します。 

   :::image type="content" source="../media/expressroute-global-reach/start-request-authorization-key.png" alt-text="ExpressRoute の承認キーを要求する方法を示すスクリーンショット。" border="true" lightbox="../media/expressroute-global-reach/start-request-authorization-key.png":::

1. これに名前を指定し、 **[作成]** を選択します。 
      
   キーの作成には約 30 秒かかることがあります。 作成された新しいキーが、プライベート クラウドの承認キーの一覧に表示されます。

   :::image type="content" source="../media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="ExpressRoute Global Reach の承認キーを示すスクリーンショット。":::
  
1. 承認キーと ExpressRoute ID をコピーします。 これらは、ピアリングを行う際に使用します。  

   > [!NOTE]
   > 承認キーはしばらくすると消えるため、表示されたらすぐにコピーしてください。