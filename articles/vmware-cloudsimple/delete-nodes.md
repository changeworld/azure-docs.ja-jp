---
title: VMware Solutions (AVS) 用のノードを削除する - Azure
description: AVS デプロイで VMware からノードを削除する方法について説明します
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024740"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Azure VMware Solution by AVS からノードを削除する

AVS ノードは、作成されると従量制で課金されます。 ノードの従量制課金を停止するには、ノードを削除する必要があります。 Azure portal から、使用されていないノードを削除します。

## <a name="before-you-begin"></a>開始する前に

ノードは、次の条件下でのみ削除できます。

* ノードと共に作成された AVS プライベート クラウドが削除されている。 AVS プライベート クラウドを削除する場合は、[Azure VMware Solution by AVS プライベート クラウドの削除](delete-private-cloud.md)に関するページを参照してください。
* AVS プライベート クラウドを縮小することによって、ノードが AVS プライベート クラウドから削除されている。 AVS プライベート クラウドを縮小する場合は、[Azure VMware Solution by AVS プライベート クラウドの縮小](shrink-private-cloud.md)に関するページを参照してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="delete-avs-node"></a>AVS ノードを削除する

1. **[すべてのサービス]** を選択します。

2. **AVS ノード**を検索します。

   ![AVS ノードの検索](media/create-cloudsimple-node-search.png)

3. **[AVS Nodes]\(AVS ノード\)** を選択します。

4. 削除する AVS プライベート クラウドに属していないノードを選択します。 **[AVS PRIVATE CLOUD NAME]\(AVS のプライベート クラウド名\)** 列に、ノードが属している AVS プライベート クラウド名が表示されます。 ノードが AVS プライベート クラウドで使用されていない場合、値は空になります。 

    ![AVS ノードを選択する](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> 削除できるのは、AVS プライベート クラウドの一部ではないノードのみです。

## <a name="next-steps"></a>次のステップ

* [AVS プライベート クラウド](cloudsimple-private-cloud.md)について確認する
