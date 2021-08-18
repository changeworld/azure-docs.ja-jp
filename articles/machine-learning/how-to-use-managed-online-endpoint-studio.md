---
title: スタジオでマネージド オンライン エンドポイント (プレビュー) を使用する
titleSuffix: Azure Machine Learning
description: Azure Machine Learning スタジオを使用して、マネージド オンライン エンドポイント (プレビュー) を作成および使用する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: how-to, managed online endpoints, devplatv2
ms.author: ssambare
author: shivanissambare
ms.reviewer: peterlu
ms.date: 05/25/2021
ms.openlocfilehash: 4f2b69a4a4009784e7943d57b0ec5653528a418e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448180"
---
# <a name="create-and-use-managed-online-endpoints-preview-in-the-studio"></a>スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する

スタジオを使用して、Azure Machine Learning でマネージド オンライン エンドポイント (プレビュー) を作成および管理する方法について説明します。 マネージド オンライン エンドポイントを使用して、運用スケールのデプロイを効率化します。 マネージド オンライン エンドポイントの詳細については、[エンドポイントの概要](concept-endpoints.md)に関するページを参照してください。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * マネージド オンライン エンドポイントを作成する
> * マネージド オンライン エンドポイントを表示する
> * マネージド オンライン エンドポイントを更新する
> * マネージド オンライン エンドポイントとデプロイを削除する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

- Azure Machine Learning ワークスペース。 詳細については、[Azure Machine Learning ワークスペースの作成](how-to-manage-workspace.md)に関するページをご覧ください。
- [ワークスペースに登録されているモデル](how-to-deploy-and-where.md#registermodel)。
- デプロイされるモデルのスコアリング ファイル。 モデルを登録し、スコアリング ファイルを作成する手順の例については、[画像の分類に関するチュートリアル](tutorial-train-models-with-aml.md)を参照してください。
- 自分のワークスペースに登録されたカスタム環境、**または** Docker コンテナー レジストリ イメージと Python 環境。 環境の詳細については、「[Azure Machine Learning でソフトウェア環境を作成して使用する](how-to-use-environments.md)」を参照してください。

## <a name="create-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイント (プレビュー) を作成する

スタジオを使用して、マネージド オンライン エンドポイント (プレビュー) をブラウザーで直接作成します。 スタジオでマネージド オンライン エンドポイントを作成する場合は、初期デプロイを定義する必要があります。 空のマネージド オンライン エンドポイントを作成することはできません。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[エンドポイント]** ページを選択します。
1. **[+ 作成 (プレビュー)]** を選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/endpoint-create-managed-online-endpoint.png" alt-text="[エンドポイント] タブでマネージド オンライン エンドポイントを作成する":::

スタジオの **[モデル]** ページでマネージド オンライン エンドポイントを作成することもできます。 これは、既存のマネージド オンライン デプロイにモデルを追加するための簡単な方法です。

1. [Azure Machine Learning Studio](https://ml.azure.com) に移動します。
1. 左側のナビゲーション バーで、 **[モデル]** ページを選択します。
1. モデル名の横にある円をチェックして、モデルを選択します。
1. **[デプロイ]**  >  **[Deploy to endpoint (preview)]\(エンドポイント (プレビュー) にデプロイする\)** の順に選択します。

セットアップ ウィザードに従って、マネージド オンライン エンドポイントを構成します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/models-page-deployment-latest.png" alt-text="[モデル] タブでマネージド オンライン エンドポイントを作成する":::

## <a name="view-managed-online-endpoints-preview"></a>マネージド オンライン エンドポイント (プレビュー) を表示する

マネージド オンライン エンドポイント (プレビュー) は、 **[エンドポイント]** ページで表示できます。 エンドポイントの詳細ページを使用して、エンドポイント URI、状態、テスト ツール、アクティビティ モニター、デプロイ ログ、サンプル使用コードなどの重要な情報を検索します。

1. 左側のナビゲーション バーで、 **[エンドポイント]** を選択します。
1. (省略可能) **マネージド** コンピューティングの種類のみを表示するために、 **[コンピューティングの種類]** に対して **フィルター** を作成します。
1. エンドポイント名を選択して、エンドポイントの詳細ページを表示します。

### <a name="test"></a>テスト

エンドポイントの詳細ページの **[テスト]** タブを使用して、自分のマネージド オンライン デプロイをテストします。 サンプル入力を入力し、結果を表示します。

1. エンドポイントの詳細ページの **[テスト]** タブを選択します。
1. ドロップダウンを使用して、テストするデプロイを選択します。
1. サンプル入力を入力します。
1. **[Test]** を選択します。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/test-deployment.png" alt-text="ブラウザーでサンプル データを直接指定してデプロイをテストする":::

### <a name="monitoring"></a>監視

**[監視]** タブを使用して、マネージド オンライン エンドポイントの高レベルのアクティビティ モニター グラフを表示します。

監視タブを使用するには、エンドポイントを作成するときに **[Enable Application Insight diagnostic and data collection]\(Application Insight の診断とデータ収集を有効にする\)** を選択する必要があります。

:::image type="content" source="media/how-to-create-managed-online-endpoint-studio/monitor-endpoint.png" alt-text="スタジオでエンドポイントレベルのメトリックを監視する":::

その他のモニターとアラートを表示する方法の詳細については、「[マネージド オンライン エンドポイントを監視する方法](how-to-monitor-online-endpoints.md)」を参照してください。

## <a name="update-managed-online-endpoints-preview"></a>マネージド オンライン エンドポイント (プレビュー) を更新する

さらにデプロイを追加し、トラフィックの割り当てを調整するために、マネージド オンライン エンドポイント (プレビュー) を更新する方法について説明します。

### <a name="add-a-managed-online-deployment"></a>マネージド オンライン デプロイを追加する

次の手順に従って、既存のマネージド オンライン エンドポイントにデプロイを追加します。

1. [エンドポイントの詳細ページ](#view-managed-online-endpoints-preview)で、 **[+ デプロイの追加]** ボタンを選択します。
2. 手順に従ってデプロイを完了します。

または、 **[モデル]** ページを使用してデプロイを追加することもできます。

1. 左側のナビゲーション バーで、 **[モデル]** ページを選択します。
1. モデル名の横にある円をチェックして、モデルを選択します。
1. **[デプロイ]**  >  **[Deploy to endpoint (preview)]\(エンドポイント (プレビュー) にデプロイする\)** の順に選択します。
1. 既存のマネージド オンライン エンドポイントにデプロイすることを選択します。

> [!NOTE]
> 新しいデプロイを追加するときに、エンドポイント内のデプロイ間のトラフィックのバランスを調整できます。
>
> :::image type="content" source="media/how-to-create-managed-online-endpoint-studio/adjust-deployment-traffic.png" alt-text="スライダーを使用して、複数のデプロイにわたるトラフィックの分散を制御する":::

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
- [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)