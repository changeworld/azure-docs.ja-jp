---
title: スタジオでバッチ エンドポイントを使用する方法
titleSuffix: Azure Machine Learning
description: この記事では、Azure Machine Learning スタジオでバッチ エンドポイントを作成する方法について説明します。 バッチ エンドポイントは、大規模なデータを継続的にバッチ スコアリングするために使用されます。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: shivanissambare
ms.author: ssambare
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.custom: how-to, studio, managed-batch-endpoints
ms.openlocfilehash: d9d6287903466283e0b94e246a17a75a77929c8a
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560589"
---
# <a name="how-to-use-batch-endpoints-preview-in-azure-machine-learning-studio"></a>Azure Machine Learning スタジオでバッチ エンドポイント (プレビュー) を使用する方法

この記事では、[Azure Machine Learning スタジオ](https://ml.azure.com)のバッチ エンドポイント (プレビュー) を使用して、バッチ スコアリングを実行する方法について説明します。 詳細については、「[Azure Machine Learning エンドポイント (プレビュー) とは](concept-endpoints.md)」を参照してください。

この記事では、次の内容について説明します。

> [!div class="checklist"]
> * MLflow モデルのバッチ エンドポイントをノー コード エクスペリエンスで作成する
> * バッチ エンドポイントの詳細を確認する
> * バッチ スコアリング ジョブを開始する
> * Azure Machine Learning スタジオのバッチ エンドポイント機能の概要

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。

* サンプル リポジトリ - [AzureML のサンプル リポジトリ](https://github.com/Azure/azureml-examples)をクローンします。 この記事では、`/cli/endpoints/batch` のアセットを使用しています。

* バッチ スコアリング ワークフローの実行場所となるコンピューティング先です。 コンピューティング先を作成する方法については、[Azure Machine Learning スタジオでのコンピューティング先の作成](how-to-create-attach-compute-studio.md)に関するページを参照してください。

* 機械学習モデルを登録します。

## <a name="create-a-batch-endpoint"></a>バッチ エンドポイントを作成する

Azure Machine Learning スタジオでバッチ エンドポイントを作成するには、次の 2 つの方法があります。

* **[エンドポイント]** ページで、 **[バッチ エンドポイント]** を選択してから **[+ 作成]** を選択します。 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-endpoints.png" alt-text="[エンドポイント] ページからバッチ エンドポイントまたはデプロイを作成するスクリーンショット":::

または

* **[モデル]** ページで、デプロイするモデルを選択し、 **[Deploy to batch endpoint (preview)]\(バッチ エンドポイントにデプロイする (プレビュー)\)** を選択します。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/models-page-deployment.png" alt-text="[モデル] ページからバッチ エンドポイントまたはデプロイを作成するスクリーンショット":::

> [!TIP]
> MLflow モデルを使用する場合は、バッチ エンドポイントのノー コード作成を利用できます。 つまり、スコアリング スクリプトと環境は、どちらも自動的に生成されるので、自分で用意する必要はありません。 詳細については、[MLflow と Azure Machine Learning を使用して ML モデルをトレーニングして追跡する方法 (プレビュー)](how-to-use-mlflow.md) に関するページを参照してください。
> 
> :::image type="content" source="media/how-to-use-batch-endpoints-studio/mlflow-model-wizard.png" alt-text="MLflow モデルをデプロイしているスクリーンショット":::

ウィザードのすべての手順を完了して、バッチ エンドポイントとデプロイを作成します。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/review-batch-wizard.png" alt-text="バッチ エンドポイントまたはデプロイのレビュー画面のスクリーンショット":::

## <a name="check-batch-endpoint-details"></a>バッチ エンドポイントの詳細を確認する

バッチ エンドポイントが作成された後に **[エンドポイント]** ページで選択することで、詳細を表示できます。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/batch-endpoint-details.png" alt-text="バッチ エンドポイントとデプロイの詳細を確認しているスクリーンショット":::

## <a name="start-a-batch-scoring-job"></a>バッチ スコアリング ジョブを開始する

バッチ スコアリング ワークロードは、オフライン ジョブとして実行されます。 既定では、バッチ スコアリングによって、スコアリング出力が Blob Storage に格納されます。 また、出力の場所を構成したり、最適なパフォーマンスを得るために一部の設定を上書きしたりすることもできます。

1. **[+ ジョブの作成]** を選択します。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/create-batch-job.png" alt-text="バッチ スコアリングを開始する [ジョブの作成] オプションのスクリーンショット":::

1. ジョブの送信中に、ドロップダウンから既定のデプロイを更新することができます。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/job-setting-batch-scoring.png" alt-text="デプロイを使用してバッチ ジョブを送信しているスクリーンショット":::

### <a name="overwrite-settings"></a>設定を上書きする

一部の設定は、バッチ スコアリング ジョブを開始するときに上書きすることができます。 たとえば、設定を上書きして、コンピューティング リソースを有効活用し、パフォーマンスを高めることができます。 設定をオーバーライドするには、 __[Override deployment settings]\(デプロイ設定のオーバーライド\)__ を選択し、設定を指定します。 詳細については、[バッチ エンドポイントの使用](how-to-use-batch-endpoint.md#configure-the-output-location-and-overwrite-settings)に関するページを参照してください。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/overwrite-setting.png" alt-text="バッチ ジョブの開始時の上書き設定のスクリーンショット":::

### <a name="start-a-batch-scoring-job-with-different-input-options"></a>異なる入力オプションでバッチ スコアリング ジョブを開始する

Azure Machine Learning スタジオには、データ入力を指定するための次の 2 つのオプションがあります。

* **登録済みデータセット** を使用する:

    > [!NOTE]
    > プレビュー期間中は、FileDataset のみサポートされます。 

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-dataset-for-job.png" alt-text="入力オプションとして登録済みデータセットを選択しているスクリーンショット":::

または

* **データストア** を使用する:

    AML に登録されているデータストアを指定するか、データが公開されている場合は、そのパブリック パスを指定します。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/select-datastore-job.png" alt-text="入力オプションとしてデータストアを選択しているスクリーンショット":::

### <a name="configure-the-output-location"></a>出力の場所を構成する

既定では、バッチ スコアリングの結果は、ワークスペースの既定の BLOB ストアに格納されます。 結果は、ジョブ名 (システムによって生成された GUID) にちなんで名前が付けられたフォルダーに格納されます。

結果の格納場所を変更するには、ジョブの開始時に、BLOB ストアと出力パスを指定します。

> [!IMPORTANT]
> 一意の出力場所を使用する必要があります。 出力ファイルが存在する場合、バッチ スコアリング ジョブでエラーが発生します。 

:::image type="content" source="media/how-to-use-batch-endpoints-studio/configure-output-location.png" alt-text="任意で出力場所を構成しているスクリーンショット":::

### <a name="summary-of-all-submitted-jobs"></a>送信されたすべてのジョブの概要

エンドポイントに対して送信されたすべてのジョブの概要を表示するには、エンドポイントを選択してから **[実行]** タブを選択します。

:::image type="content" source="media/how-to-use-batch-endpoints-studio/summary-jobs.png" alt-text="バッチ エンドポイントに送信されたジョブの概要のスクリーンショット":::
## <a name="check-batch-scoring-results"></a>バッチ スコアリングの結果を確認する

スコアリング結果を表示する方法については、[バッチ エンドポイントを使用する](how-to-use-batch-endpoint.md#check-batch-scoring-results)方法に関するページを参照してください。

## <a name="add-a-deployment-to-an-existing-batch-endpoint"></a>デプロイを既存のバッチ エンドポイントに追加する

Azure Machine Learning スタジオには、既存のバッチ エンドポイントにデプロイを追加する方法が 2 つあります。

* **[エンドポイント]** ページで、新しいデプロイを追加するバッチ エンドポイントを選択します。 **[+ デプロイの追加]** を選択したら、ウィザードを完了して新しいデプロイを追加します。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-option.png" alt-text="新しいデプロイの追加オプションのスクリーンショット":::

または

* **[モデル]** ページで、デプロイするモデルを選択します。 次に、ドロップダウンから、 **[Deploy to batch endpoint (preview)]\(バッチ エンドポイントにデプロイする (プレビュー)\)** オプションを選択します。 ウィザードの **[エンドポイント]** 画面で、 **[既存]** を選択します。 ウィザードを完了して、新しいデプロイを作成します。

    :::image type="content" source="media/how-to-use-batch-endpoints-studio/add-deployment-models-page.png" alt-text="既存のバッチ エンドポイントを選択して新しいデプロイを追加しているスクリーンショット":::

## <a name="update-the-default-deployment"></a>既定のデプロイを更新する

エンドポイントに複数のデプロイがある場合、そのデプロイの内 1 つが *既定* です。 既定のデプロイは、エンドポイントへのトラフィックの 100% を受け取ります。 既定のデプロイを変更するには、次の手順に従います。

1. **[エンドポイント]** ページで、エンドポイントを選択します。
1. **[Update default deployment]\(既定のデプロイを更新\)** を選択します。 **[詳細]** タブで、既定として設定するデプロイを選択し、 **[更新]** を選択します。
    :::image type="content" source="media/how-to-use-batch-endpoints-studio/update-default-deployment.png" alt-text="既定のデプロイを更新しているスクリーンショット":::

## <a name="delete-batch-endpoint-and-deployments"></a>バッチ エンドポイントとデプロイを削除する

**エンドポイント** を削除するには、 **[エンドポイント]** ページでエンドポイントを選択してから、削除を選択します。

> [!WARNING]
> エンドポイントを削除すると、そのエンドポイントへのデプロイもすべて削除されます。

**デプロイ** を削除するには、 **[エンドポイント]** ページでエンドポイントを選択し、デプロイを選択してから、削除を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、バッチ エンドポイントを作成して呼び出す方法について説明しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [バッチ エンドポイントのトラブルシューティング](how-to-troubleshoot-batch-endpoints.md)
* [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)](how-to-deploy-managed-online-endpoints.md)
