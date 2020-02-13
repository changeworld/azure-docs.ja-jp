---
title: VMware Solutions (AVS) 用のノードをプロビジョニングする - Azure
description: AVS 展開時に VMWare にノードを追加する方法について説明します
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024808"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Azure VMware Solutions (AVS) 用のノードをプロビジョニングする

Azure portal における VM のプロビジョニング 次に、AVS プライベート クラウド環境に従量課金の容量を設定できます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="add-a-node-to-your-avs-private-cloud"></a>AVS プライベート クラウドにノードを追加する

1. **[すべてのサービス]** を選択します。
2. **AVS ノード**を検索します。

   ![AVS ノードの検索](media/create-cloudsimple-node-search.png)

3. **[AVS Nodes]\(AVS ノード\)** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![AVS ノードの追加](media/create-cloudsimple-node-add.png)

5. AVS ノードをプロビジョニングするサブスクリプションを選択します。
6. ノードのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. ノードを識別するためにプレフィックスを入力します。
8. ノード リソースの場所を選択します。
9. ノード リソースをホスティングする専用の場所を選択します。
10. [ノードの種類](cloudsimple-node.md)を選択します。
11. プロビジョニングするノードの数を選択します。
12. **[確認および作成]** を選択します。
13. 設定を確認します。 設定を変更素ウルには、 **[前へ]** をクリックします。
14. **作成** を選択します。

## <a name="next-steps"></a>次のステップ

* [AVS プライベート クラウドの作成](create-private-cloud.md)
