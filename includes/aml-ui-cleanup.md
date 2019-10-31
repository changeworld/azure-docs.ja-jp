---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692353"
---
>[!IMPORTANT]
>作成したリソースは、Azure Machine Learning service のその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

### <a name="delete-everything"></a>すべてを削除する

作成したものを今後使用する予定がない場合は、課金が発生しないように、リソース グループ全体を削除します。

1. Azure portal で、ウィンドウの左側にある **[リソース グループ]** を選択します。
 
   ![Azure portal でリソース グループを削除する](./media/aml-ui-cleanup/delete-resources.png)

1. 一覧から、作成したリソース グループを選択します。

1. ウィンドウの右側で、省略記号ボタン ( **...** ) を選択します。

1. **[リソース グループの削除]** を選択します。

リソース グループを削除すると、ビジュアル インターフェイスで作成したすべてのリソースも削除されます。  

### <a name="delete-only-the-compute-target"></a>コンピューティング ターゲットのみを削除する

ここで作成したコンピューティング ターゲットは、使用されていない場合、自動的にゼロ  ノードに*自動スケーリング*されます。 これは、料金を最小限に抑えるためです。 コンピューティング ターゲットを削除する場合は、次の手順を実行してください。

1. [Azure portal](https://portal.azure.com) でワークスペースを開きます。

    ![コンピューティング ターゲットを削除する](./media/aml-ui-cleanup/delete-compute-target.png)

1. **コンピューティング**セクションで、ワークスペースのリソースを選択します。

1. **[削除]** を選択します。

### <a name="delete-individual-assets"></a>個々の資産を削除する

実験を作成したビジュアル インターフェイスで、個々の資産を選択し、 **[削除]** ボタンを選択してそれらを削除します。 ワークスペースからデータセットを登録解除するには、各データセットを選択し、 **[登録解除]** を選択します。

![アセットを削除する](./media/aml-ui-cleanup/delete-asset.png)
