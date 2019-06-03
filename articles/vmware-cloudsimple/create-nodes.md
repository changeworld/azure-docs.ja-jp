---
title: CloudSimple による VMware ソリューション - Azure
description: CloudSimple 展開時に VMWare にノードを追加する方法について説明します
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9f5108207c4f8debc65b9d1fe625ae7033ca4e70
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576837"
---
# <a name="purchase-nodes-for-vmware-solution-by-cloudsimple---azure"></a>CloudSimple による VMware ソリューション - Azure

Azure portal における VM のプロビジョニング 次に、CloudSimple プライベート クラウド環境に従量課金の容量をセットアップできます。

## <a name="add-a-purchased-node-to-your-cloudsimple-private-cloud"></a>CloudSimple プライベート クラウドを に購入したノードを追加します。

1. **[すべてのサービス]** を選択します。
2. **CloudSimple ノード** を検索します。

   ![CloudSimple ノードを検索します。](media/create-cloudsimple-node-search.png)

3. **CloudSimple ノード** を選択します。
4. **[追加]** をクリックして、ノードを作成します。

    ![CloudSimple ノードを追加します。](media/create-cloudsimple-node-add.png)

5. CloudSimple ノードを購入するサブスクリプションを選択します。
6. ノードのリソース グループを選択します。 リソース グループを新規に追加するには、 **[Create New]** (新規作成) をクリックします。
7. ノードを識別するためにプレフィックスを入力します。
8. ノード リソースの場所を選択します。
9. ノード リソースをホスティングする専用の場所を選択します。
10. ノードの種類を選択します。 [CS28 または CS36 のオプション](cloudsimple-node.md)を選択できます。 後者のオプションには、コンピューティングとメモリの最大容量が含まれています。
11. プロビジョニングするノードの数を選択します。
12. **[確認および作成]** を選択します。
13. 設定を確認します。 設定を変更素ウルには、 **[前へ]** をクリックします。
14. **作成** を選択します。

## <a name="next-steps"></a>次の手順

* [プライベート クラウドの作成](https://docs.azure.cloudsimple.com/create-private-cloud/)
