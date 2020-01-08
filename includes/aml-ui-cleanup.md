---
title: インクルード ファイル
description: インクルード ファイル
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659876"
---
>[!IMPORTANT]
>作成したリソースは、Azure Machine Learning のその他のチュートリアルおよびハウツー記事の前提条件として使用できます。

### <a name="delete-everything"></a>すべてを削除する

作成したすべてのものを使用する予定がない場合は、料金が発生しないように、リソース グループ全体を削除します。

1. Azure portal で、ウィンドウの左側にある **[リソース グループ]** を選択します。
 
   ![Azure portal でリソース グループを削除する](./media/aml-ui-cleanup/delete-resources.png)

1. 一覧から、作成したリソース グループを選択します。

1. **[リソース グループの削除]** を選択します。

リソース グループを削除すると、デザイナーで作成したすべてのリソースも削除されます。 

### <a name="delete-individual-assets"></a>個々の資産を削除する

実験を作成したデザイナーで、個々の資産を選択し、 **[削除]** ボタンを選択してそれらを削除します。

ここで作成したコンピューティング ターゲットは、使用されていない場合、自動的にゼロ  ノードに*自動スケーリング*されます。 このアクションは、料金を最小限に抑えるために実行されます。 コンピューティング ターゲットを削除する場合は、次の手順を実行してください。

![アセットを削除する](./media/aml-ui-cleanup/delete-asset.png)

各データセットを選択し、 **[登録解除]** を選択することによって、ワークスペースからデータセットを登録解除できます。

![データセットの登録解除](./media/aml-ui-cleanup/unregister-dataset1225.png)

データセットを削除するには、Azure portal または Azure Storage Explorer を使用してストレージ アカウントに移動し、これらのアセットを手動で削除します。


