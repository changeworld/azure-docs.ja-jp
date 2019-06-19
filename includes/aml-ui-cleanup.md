---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181592"
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

実験を作成したビジュアル インターフェイスで、個々の資産を選択し、 **[削除]** ボタンを選択してそれらを削除します。

![実験を削除する](./media/aml-ui-cleanup/delete-experiment.png)
