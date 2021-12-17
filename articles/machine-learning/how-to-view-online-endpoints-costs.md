---
title: マネージド オンライン エンドポイント (プレビュー) のコストを表示する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning のマネージド オンライン エンドポイントのコストを表示する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.date: 05/03/2021
ms.topic: conceptual
ms.custom: how-to, deploy, devplatv2
ms.openlocfilehash: bb07d499007cbbc0cb502611056aaef583cbb16a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588810"
---
# <a name="view-costs-for-an-azure-machine-learning-managed-online-endpoint-preview"></a>Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する

マネージド オンライン エンドポイント (プレビュー) のコストを表示する方法について説明します。 エンドポイントのコストは、関連付けられているワークスペースで発生します。 タグを使用して、特定のエンドポイントのコストを確認できます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

> [!IMPORTANT]
> この記事は、Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストの表示にのみ適用されます。 マネージド オンライン エンドポイントは、コストを追跡するためにタグを使用する必要があるという点で、他のリソースとは異なります。 他の Azure リソースのコストを表示する方法の詳細については、「[クイックスタート: コスト分析を使用してコストを調査および分析する](../cost-management-billing/costs/quick-acm-cost-analysis.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) をデプロイします。
- エンドポイントがデプロイされるサブスクリプションに対して、少なくとも[請求閲覧者](../role-based-access-control/role-assignments-portal.md)のアクセス権を取得する

## <a name="view-costs"></a>コストを表示する

サブスクリプションの **[コスト分析]** ページに移動します。

1. [Azure portal](https://portal.azure.com) で、サブスクリプションの **[コスト分析]** を選択します。

    [![マネージド オンライン エンドポイントのコスト分析: 左側の [コスト分析] ボタンの周囲に赤い四角形が表示されている、Azure portal でのサブスクリプションのスクリーンショット。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis.png#lightbox)

データのスコープを Azure Machine Learning ワークスペース リソースに設定するフィルターを作成します。

1. 上部のナビゲーション バーで、 **[フィルターの追加]** を選択します。

1. 最初のフィルター ドロップダウンで、フィルターの種類として **[リソース]** を選択します。

1. 2 つ目のフィルター ドロップダウンで、Azure Machine Learning ワークスペースを選択します。

    [![マネージド オンライン エンドポイントのコスト分析: 右上の [フィルターの追加] ボタンの周囲に赤い四角形が表示されている、[コスト分析] ビューのスクリーンショット。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-add-filter.png#lightbox)

タグ フィルターを作成して、マネージド オンライン エンドポイントおよびマネージド オンライン デプロイを表示します。
1. **[フィルターの追加]**  >  **[タグ]**  >  **[azuremlendpoint**: "\<your endpoint name>"] の順に選択します 
1. **[フィルターの追加]**  >  **[タグ]**  >  **[azuremldeployment**: "\<your deployment name>"] の順に選択します。

    > [!NOTE]
    > この画像のドル値は架空のものであり、実際のコストは反映していません。

    [![マネージド オンライン エンドポイントのコスト分析: 右上の [タグ] ボタンの周囲に赤い四角形が表示されている、[コスト分析] ビューのスクリーンショット。](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png)](./media/how-to-view-online-endpoints-costs/online-endpoints-cost-analysis-select-endpoint-deployment.png#lightbox)

## <a name="next-steps"></a>次の手順
- [エンドポイントとは何ですか。](concept-endpoints.md)
- [マネージド オンライン エンドポイントを監視する](./how-to-monitor-online-endpoints.md)方法について学習します。
- [Azure CLI を使用してマネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)
- [スタジオを使用してマネージド オンライン エンドポイントをデプロイする方法](how-to-use-managed-online-endpoint-studio.md)