---
title: スタジオでマネージド オンライン エンドポイント (プレビュー) を使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオを使用して、マネージド オンライン エンドポイント (プレビュー) を作成および使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2, studio
ms.author: ssambare
author: shivanissambare
ms.reviewer: laobri
ms.date: 10/21/2021
ms.openlocfilehash: fee2a8211b90c2b7dbc06a1e64f047e28031eaa6
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560382"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する

スタジオを使用して、Azure Machine Learning でマネージド オンライン エンドポイント (プレビュー) を作成および管理する方法について説明します。 マネージド オンライン エンドポイントを使用して、運用スケールのデプロイを効率化します。 マネージド オンライン エンドポイントの詳細については、[エンドポイントの概要](concept-endpoints.md)に関するページを参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * マネージド オンライン エンドポイントを作成する
> * マネージド オンライン エンドポイントを表示する
> * マネージド オンライン エンドポイントにデプロイを追加する
> * マネージド オンライン エンドポイントを更新する
> * マネージド オンライン エンドポイントとデプロイを削除する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件
- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- サンプル リポジトリ - [AzureML のサンプル リポジトリ](https://github.com/Azure/azureml-examples)をクローンします。 この記事では、`/cli/endpoints/online` のアセットを使用しています。

## <a name="create-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイント (プレビュー) を作成する

スタジオを使用して、マネージド オンライン エンドポイント (プレビュー) をブラウザーで直接作成します。 スタジオでマネージド オンライン エンドポイントを作成する場合は、初期デプロイを定義する必要があります。 空のマネージド オンライン エンドポイントを作成することはできません。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[エンドポイント]** ページを選択します。
1. **[+ 作成 (プレビュー)]** を選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="[エンドポイント] タブからマネージド オンライン エンドポイントを作成する様子を示すスクリーンショット。":::

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" lightbox="media/how-to-create-managed-online-endpoint-studio/online-endpoint-wizard.png" alt-text="マネージド オンライン エンドポイント作成ウィザードのスクリーンショット。":::

### <a name="follow-the-setup-wizard-to-configure-your-managed-online-endpoint"></a>セットアップ ウィザードに従って、マネージド オンライン エンドポイントを構成します。

1. サンプル [モデル](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1/model)と[スコアリング スクリプト](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)を [https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/model-1) から使用できます。
1. ウィザードの **[環境]** ステップで、**AzureML-sklearn-0.24.1-ubuntu18.04-py37-cpu-inference** キュレーション環境を選択できます。

スタジオの **[モデル]** ページでマネージド オンライン エンドポイントを作成することもできます。 これは、既存のマネージド オンライン デプロイにモデルを追加するための簡単な方法です。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[モデル]** ページを選択します。
1. モデル名の横にある円をチェックして、モデルを選択します。
1. **[デプロイ]**  >  **[リアルタイム エンドポイントへのデプロイ (プレビュー)]** を選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/deploy-from-models-page.png" alt-text="Models UI からマネージド オンライン エンドポイントを作成する様子を示すスクリーンショット。":::

## <a name="view-managed-online-endpoints-preview"></a>マネージド オンライン エンドポイント (プレビュー) を表示する

マネージド オンライン エンドポイント (プレビュー) は、 **[エンドポイント]** ページで表示できます。 エンドポイントの詳細ページを使用して、エンドポイント URI、状態、テスト ツール、アクティビティ モニター、デプロイ ログ、サンプル使用コードなどの重要な情報を検索します。

1. 左側のナビゲーション バーで、 **[エンドポイント]** を選択します。
1. (省略可能) **マネージド** コンピューティングの種類のみを表示するために、 **[コンピューティングの種類]** に対して **フィルター** を作成します。
1. エンドポイント名を選択して、エンドポイントの詳細ページを表示します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/managed-endpoint-details-page.png" alt-text="マネージド エンドポイントの詳細ビューのスクリーンショット。":::

### <a name="test"></a>テスト

エンドポイントの詳細ページの **[テスト]** タブを使用して、自分のマネージド オンライン デプロイをテストします。 サンプル入力を入力し、結果を表示します。

1. エンドポイントの詳細ページの **[テスト]** タブを選択します。
1. ドロップダウンを使用して、テストするデプロイを選択します。
1. サンプル入力を入力します。
1. **[Test]** を選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" lightbox="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="ブラウザーでサンプル データを直接指定してデプロイをテストする様子を示すスクリーンショット。":::

### <a name="monitoring"></a>監視

**[監視]** タブを使用して、マネージド オンライン エンドポイントの高レベルのアクティビティ モニター グラフを表示します。

監視タブを使用するには、エンドポイントを作成するときに **[Enable Application Insight diagnostic and data collection]\(Application Insight の診断とデータ収集を有効にする\)** を選択する必要があります。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" lightbox="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="スタジオでエンドポイント レベルのメトリックを監視する様子を示すスクリーンショット。":::

その他のモニターとアラートを表示する方法の詳細については、「[マネージド オンライン エンドポイントを監視する方法](how-to-monitor-online-endpoints.md)」を参照してください。

## <a name="add-a-deployment-to-a-managed-online-endpoint"></a>マネージド オンライン エンドポイントにデプロイを追加する

既存のマネージド オンライン エンドポイントにデプロイを追加できます。

**エンドポイントの詳細ページ** から以下を行います

1. [エンドポイントの詳細ページ](#view-managed-online-endpoints-preview)で、 **[+ デプロイの追加]** ボタンを選択します。
2. 手順に従ってデプロイを完了します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" lightbox="media/how-to-create-managed-online-endpoint-studio/add-deploy-option-from-endpoint-page.png" alt-text="[エンドポイントの詳細] ページの [デプロイオプションの追加] のスクリーンショット。":::

または、 **[モデル]** ページを使用してデプロイを追加することもできます。

1. 左側のナビゲーション バーで、 **[モデル]** ページを選択します。
1. モデル名の横にある円をチェックして、モデルを選択します。
1. **[デプロイ]**  >  **[リアルタイム エンドポイントへのデプロイ (プレビュー)]** を選択します。
1. 既存のマネージド オンライン エンドポイントにデプロイすることを選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" lightbox="media/how-to-create-managed-online-endpoint-studio/select-existing-managed-endpoints.png" alt-text="[モデル] ページの [デプロイの追加] オプションのスクリーンショット。":::

> [!NOTE]
> 新しいデプロイを追加するときに、エンドポイント内のデプロイ間のトラフィックのバランスを調整できます。
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" lightbox="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="スライダーを使用して、複数のデプロイにわたるトラフィックの分散を制御する方法を示すスクリーンショット。":::

## <a name="update-managed-online-endpoints-preview"></a>マネージド オンライン エンドポイント (プレビュー) を更新する

デプロイ トラフィックの割合とインスタンス数は、Azure Machine Learning スタジオから更新できます。

### <a name="update-deployment-traffic-allocation"></a>デプロイのトラフィックの割り当てを更新する

**デプロイのトラフィックの割り当て** を使用して、エンドポイント内の各デプロイに送信される要求の受信の割合を制御します。

1. エンドポイントの詳細ページで、 **[トラフィックを更新する]** を選択します。
2. トラフィックを調整し、 **[更新]** を選択します。

> [!TIP]
> **[Total traffic percentage]\(トラフィックの割合の合計\)** は、0% (トラフィックを無効にする場合) または 100% (トラフィックを有効にする場合) になる必要があります。

### <a name="update-deployment-instance-count"></a>デプロイのインスタンス数を更新する

個々のデプロイを、インスタンス数を調整してスケールアップまたはスケールダウンするには、次の手順に従います。

1. エンドポイントの詳細ページで。 更新するデプロイのカードを見つけます。
1. デプロイの詳細カードの **編集アイコン** を選択します。
1. インスタンス数を更新します。
1. **[更新]** を選択します。

## <a name="delete-managed-online-endpoints-and-deployments-preview"></a>マネージド オンライン エンドポイントとデプロイ (プレビュー) を削除する

マネージド オンライン エンドポイント (プレビュー) 全体と、それに関連付けられているデプロイ (プレビュー) を削除する方法について説明します。 または、マネージド オンライン エンドポイントから個々のデプロイを削除します。

### <a name="delete-a-managed-online-endpoint"></a>マネージド オンライン エンドポイントを削除する

マネージド オンライン エンドポイントを削除すると、それに関連付けられているすべてのデプロイも削除されます。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[エンドポイント]** ページを選択します。
1. モデル名の横にある円をチェックして、エンドポイントを選択します。
1. **[削除]** を選択します。

または、[エンドポイントの詳細ページ](#view-managed-online-endpoints-preview)で、マネージド オンライン エンドポイントを直接削除することもできます。 

### <a name="delete-an-individual-deployment"></a>個々のデプロイを削除する

次の手順に従って、マネージド オンライン エンドポイントから個々のデプロイを削除します。 これは、マネージド オンライン エンドポイント内の他のデプロイに影響します。

> [!NOTE]
> トラフィックが割り当てられているデプロイを削除することはできません。 デプロイを削除する前に、まず、デプロイの[トラフィックの割り当ての設定](#update-deployment-traffic-allocation)を 0% にする必要があります。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[エンドポイント]** ページを選択します。
1. 自分のマネージド オンライン エンドポイントを選択します。
1. エンドポイントの詳細ページで、削除したいデプロイを探します。
1. **削除アイコン** を選択します。

## <a name="next-steps"></a>次の手順

この記事では、Azure Machine Learning のマネージド オンライン エンドポイントを使用する方法について説明しました。 これらの次のステップを参照してください。

- [エンドポイントとは何ですか。](concept-endpoints.md)
- [Azure CLI を使用してマネージド オンライン エンドポイントをデプロイする方法](how-to-deploy-managed-online-endpoints.md)
- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [マネージド オンライン エンドポイントを監視する方法](how-to-monitor-online-endpoints.md)
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](./how-to-troubleshoot-online-endpoints.md)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)