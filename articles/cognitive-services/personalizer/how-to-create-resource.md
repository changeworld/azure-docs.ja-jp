---
title: Personalizer リソースを作成する
description: サービス構成には、サービスによる報酬の処理方法、サービスによる探索の頻度、モデルの再トレーニング頻度、格納するデータ量などがあります。
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624185"
---
# <a name="create-a-personalizer-resource"></a>Personalizer リソースを作成する

Personalizer リソースは、Personalizer の学習ループと同じものです。 単一リソース (学習ループ) は、存在する各サブジェクト ドメインまたはコンテンツ領域に対して作成されます。 同じループ内で複数のコンテンツ領域を使用しないでください。そのようにすると、学習ループを混乱させ、予測精度が低下するためです。

Personalizer で Web ページの複数のコンテンツ領域に最適なコンテンツを選択できるようにするには、それぞれに異なる学習ループを使用します。


## <a name="create-a-resource-in-the-azure-portal"></a>Azure portal でリソースを作成する

フィードバック ループごとに Personalizer リソースを作成します。

1. [Azure ポータル](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)にサインインします。 前のリンクから、Personalizer サービスの**作成**ページに移動できます。
1. サービス名を入力し、サブスクリプション、場所、価格レベル、リソース グループを選択します。

    > [!div class="mx-imgBorder"]
    > ![Azure portal を使用して、学習ループとも呼ばれる、Personalizer リソースを作成する。](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. **[作成]** を選択して、リソースを作成します。

1. リソースがデプロイされたら、 **[リソースに移動]** ボタンを選択して、Personalizer リソースに移動します。 新しいリソースの **[構成]** ページに移動して、[学習ループを構成](how-to-settings.md)します。

## <a name="create-a-resource-with-the-azure-cli"></a>Azure CLI を使用してリソースを作成する

1. 次のコマンドを使用して、Azure CLI にサインインします。

    ```bash
    az login
    ```

1. リソース グループを作成します。これは、Personalizer リソースで使用する予定のすべての Azure リソースを管理するための論理グループです。


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. 既存のリソース グループに対して次のコマンドを使用して、新しい Personalizer リソースである、"_学習ループ_" を作成します。

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>次のステップ

* Personalizer の学習ループを[構成する](how-to-settings.md)