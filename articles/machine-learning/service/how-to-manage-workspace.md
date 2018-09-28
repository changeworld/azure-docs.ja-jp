---
title: Azure Machine Learning ワークスペースを作成し、管理する
description: Azure portal で Azure Machine Learning ワークスペースを作成、表示、削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954349"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning ワークスペースを作成し、管理する

この記事では、[Azure Machine Learning サービス](overview-what-is-azure-ml.md)用の Azure portal で [**Azure Machine Learning ワークスペース**](concept-azure-machine-learning-architecture.md#workspace)を作成、表示、削除します。  [CLI](reference-azure-machine-learning-cli.md) または [Python コード](http://aka.ms/aml-sdk)を利用してワークスペースを作成したり、削除したりすることもできます。

ワークスペースを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-workspace"></a>ワークスペースの作成 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>ワークスペースの表示

1. ポータルの左上隅にある **[すべてのサービス]** を選択します。 

1. **[すべてのサービス]** フィルター フィールドに「**Machine Learning Workspace**」と入力します。  

   ![Azure Machine Learning ワークスペースの検索](media/how-to-manage-workspace/allservices-search1.png)

1. フィルターの結果から **[Machine Learning ワークスペース]** を選択すると、ワークスペースの一覧が表示されます。 

   ![Azure Machine Learning ワークスペースの検索](media/how-to-manage-workspace/allservices-search.PNG)

1. 見つかったワークスペースの一覧にひととおり確認します。 サブスクリプション、リソース グループ、場所に基づいてフィルター処理できます。  

   ![Azure Machine Learning ワークスペースの一覧](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. 先ほど作成したワークスペースを選択すると、そのプロパティが表示されます。

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>ワークスペースを削除する

削除するワークスペースの上にある [削除] ボタンを使用します。

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>リソースのクリーンアップ 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>次の手順

ワークスペースを使用し、Azure Machine Learning サービスでモデルをビルド、トレーニング、デプロイする方法については、完全版のチュートリアルをご利用ください。

> [!div class="nextstepaction"]
> [チュートリアル: モデルをトレーニングする](tutorial-train-models-with-aml.md)
