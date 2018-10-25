---
title: Azure Machine Learning Services ワークスペースを作成し、管理する
description: Azure portal 内で Azure Machine Learning Services ワークスペースを作成、表示、削除する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: c0bb27dccdaf25da818d5d54a8634556a95da737
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238679"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Azure Machine Learning Services ワークスペースを作成し、管理する

この記事では、[Azure Machine Learning Services](overview-what-is-azure-ml.md) 用の Azure portal 内で [**Azure Machine Learning Services ワークスペース**](concept-azure-machine-learning-architecture.md#workspace)を作成、表示、削除します。  [CLI](reference-azure-machine-learning-cli.md) または [Python コード](http://aka.ms/aml-sdk)を利用してワークスペースを作成したり、削除したりすることもできます。

ワークスペースを作成するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-workspace"></a>ワークスペースの作成 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>ワークスペースの表示

1. ポータルの左上隅にある **[すべてのサービス]** を選択します。 

1. **[すべてのサービス]** フィルター フィールドに、「**Machine Learning Services ワークスペース**」と入力します。  

   ![Machine Learning Services ワークスペースを検索する](media/how-to-manage-workspace/allservices-search1.png)

1. フィルターの結果から **[Machine Learning service workspace]\(Machine Learning Services ワークスペース\)** を選択すると、ご利用のワークスペースの一覧が表示されます。 

   ![Machine Learning Services ワークスペースを検索する](media/how-to-manage-workspace/allservices-search.PNG)

1. 見つかったワークスペースの一覧にひととおり確認します。 サブスクリプション、リソース グループ、場所に基づいてフィルター処理できます。  

   ![Machine Learning Services ワークスペースの一覧](media/how-to-manage-workspace/allservices_view_workspace.PNG)

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
