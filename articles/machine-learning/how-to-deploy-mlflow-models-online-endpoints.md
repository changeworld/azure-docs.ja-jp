---
title: MLflow モデルをマネージド オンライン エンドポイントにデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: Azure によって自動的に管理される Web サービスとして MLflow モデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
author: shivanissambare
ms.date: 11/03/2021
ms.topic: how-to
ms.reviewer: larryfr
ms.custom: deploy, mlflow, devplatv2, no-code-deployment
ms.openlocfilehash: a2328490185032e805a95f85b430d6742c76435c
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289890"
---
# <a name="deploy-mlflow-models-to-managed-online-endpoint-preview"></a>MLflow モデルをマネージド オンライン エンドポイントにデプロイする (プレビュー)

この記事では、[MLflow](https://www.mlflow.org) モデルを[マネージド オンライン エンドポイント](concept-endpoints.md#managed-online-endpoints)にデプロイする (プレビュー) 方法について説明します。 MLflow モデルをマネージド オンライン エンドポイントにデプロイする場合は、コードなしのデプロイになります。 スコアリング スクリプトと環境は必要ありません。 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>必須コンポーネント

[!INCLUDE [basic cli prereqs](../../includes/machine-learning-cli-prereqs.md)]

* MLflow モデルが必要です。 この記事の例は、[https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow) のモデルに基づいています。

[!INCLUDE [clone repo & set defaults](../../includes/machine-learning-cli-prepare.md)]

この記事で使用するこのコード スニペットでは、作成して使用するエンドポイントの名前が `ENDPOINT_NAME` 環境変数に含まれています。 これを設定するには、CLI で次のコマンドを使用します。 `<YOUR_ENDPOINT_NAME>` は、お使いのエンドポイントの名前に置き換えます。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="set_endpoint_name":::

## <a name="deploy-using-cli-v2"></a>CLI (v2) を使用してデプロイする

この例では、CLI (v2) を使用して MLflow モデルをマネージド オンライン エンドポイントにデプロイする方法を示します。

> [!IMPORTANT]
> MLflow のコードなしのデプロイの場合、 **[ローカル エンドポイントを使用したテスト](how-to-deploy-managed-online-endpoints.md#deploy-and-debug-locally-by-using-local-endpoints)** は現在サポートされていません。

1. エンドポイントの YAML 構成ファイルを作成します。 次の例では、エンドポイントの名前と認証モードを構成します。

    __create-endpoint.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/create-endpoint.yaml":::

1. YAML 構成を使用して新しいエンドポイントを作成するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_endpoint":::

1. デプロイの YAML 構成ファイルを作成します。 次の例では、前の手順で作成したエンドポイントへの `sklearn-diabetes` モデルのデプロイを構成します。

    > [!IMPORTANT]
    > MLflow のコードなしのデプロイ (NCD) が機能するには、 **`model_format`** を **`mlflow`** に設定することが必須です。 詳細については、「[CLI (v2) モデル YAML スキーマ](reference-yaml-model.md)」を参照してください。

    __sklearn-deployment.yaml__

    :::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sklearn-deployment.yaml":::

1. YAML 構成を使用してデプロイを作成するには、次のコマンドを使用します。

    :::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="create_sklearn_deployment":::

### <a name="invoke-the-endpoint"></a>エンドポイントを呼び出す

デプロイが完了したら、次のコマンドを使用して、デプロイされたエンドポイントにスコアリング要求を行います。 このコマンドで使用する [sample-request-sklearn.json](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/mlflow/sample-request-sklearn.json) ファイルは、azure-examples リポジトリの `/cli/endpoints/online/mlflow` ディレクトリにあります。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="test_sklearn_deployment":::

**sample-request-sklearn.json**

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/mlflow/sample-request-sklearn.json":::

応答は次のテキストのようになります。

```json
[ 
  11633.100167144921,
  8522.117402884991
]
```

### <a name="delete-endpoint"></a>エンドポイントを削除する

エンドポイントを使い終えたら、次のコマンドを使用して削除します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-mlflow.sh" ID="delete_endpoint":::

## <a name="deploy-using-azure-machine-learning-studio"></a>Azure Machine Learning スタジオを使用してデプロイする

この例では、[Azure Machine Learning スタジオ](https://ml.azure.com)を使用して MLflow モデルをマネージド オンライン エンドポイントにデプロイする方法を示します。

1. 次の YAML および CLI コマンドを使用して、モデルを MLflow 形式で登録します。 YAML では、[https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow](https://github.com/Azure/azureml-examples/tree/main/cli/endpoints/online/mlflow) の scikit-learn MLflow モデルを使用します。

    __sample-create-mlflow-model.yaml__

    ```yaml
    $schema: https://azuremlschemas.azureedge.net/latest/model.schema.json
    name: sklearn-diabetes-mlflow
    version: 1
    local_path: sklearn-diabetes/model
    model_format: mlflow
    description: Scikit-learn MLflow model.
    ```


    ```azurecli
    az ml model create -f sample-create-mlflow-model.yaml
    ```

2. [スタジオ](https://ml.azure.com)から、ワークスペースを選択し、 __[エンドポイント]__ または __[Models]\(モデル\)__ ページを使用してエンドポイントのデプロイを作成します。

    # <a name="endpoints-page"></a>[[エンドポイント] ページ](#tab/endpoint)

    1. __[エンドポイント]__ ページで、 **[+ 作成 (プレビュー)]** を選択します。

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/create-from-endpoints.png" alt-text="[エンドポイント] UI ページの [作成] オプションを示すスクリーンショット。":::

    1. エンドポイントの名前と認証の種類を指定し、 __[次へ]__ を選択します。
    1. モデルを選択する場合は、前に登録した MLflow モデルを選択します。 __[次へ]__ をクリックして続行します。

    1. MLflow 形式で登録されたモデルを選択すると、ウィザードの [環境] ステップではスコアリング スクリプトと環境は必要ありません。

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/ncd-wizard.png" alt-text="MLflow モデルに必要なコードと環境がないことを示すスクリーンショット":::

    1. ウィザードを完了して、モデルをエンドポイントにデプロイします。

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/review-screen-ncd.png" alt-text="NCD レビュー画面を示すスクリーンショット":::

    # <a name="models-page"></a>[[Models]\(モデル\) ページ](#tab/models)

    1. MLflow モデルを選択し、 __[展開する]__ を選択します。 プロンプトが表示されたら、 __[リアルタイム エンドポイントへのデプロイ (プレビュー)]__ を選択します。

        :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/deploy-from-models-ui.png" alt-text="[Models]\(モデル\) UI からモデルをデプロイする方法を示すスクリーンショット":::

    1. ウィザードを完了して、モデルをエンドポイントにデプロイします。

    ---

## <a name="deploy-models-after-a-training-job"></a>トレーニング ジョブの後にモデルをデプロイする

このセクションは、[トレーニング ジョブ](how-to-train-cli.md)を完了した後にモデルをマネージド オンライン エンドポイントにデプロイする方法を理解できるようにします。

1. トレーニング ジョブから出力をダウンロードします。 出力にはモデル フォルダーが含まれています。

    > [!NOTE]
    > トレーニング スクリプトで `mlflow.autolog()` を使用した場合は、ジョブの実行履歴にモデルの成果物が表示されます。 Azure Machine Learning は、MLflow の追跡機能と統合されます。 いくつかの一般的な ML フレームワークに `mlflow.autolog()` を使用して、モデル パラメーター、パフォーマンス メトリック、モデル成果物、さらには特徴の重要度のグラフをログに記録できます。
    >
    > 詳細については、[CLI を使用したモデルのトレーニング](how-to-train-cli.md#model-tracking-with-mlflow)に関する記事を参照してください。 また、GitHub リポジトリ内の[トレーニング ジョブのサンプル](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/single-step)も参照してください。

    # <a name="azure-machine-learning-studio"></a>[Azure Machine Learning Studio](#tab/studio)

    :::image type="content" source="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" lightbox="media/how-to-deploy-mlflow-models-online-endpoints/download-output-logs.png" alt-text="実験の実行からの出力とログをダウンロードする方法を示すスクリーンショット":::

    # <a name="cli"></a>[CLI](#tab/cli)

    ```azurecli
    az ml job download -n $run_id --outputs
    ```

2. ダウンロードしたファイルを使用してデプロイする場合は、スタジオまたは Azure コマンドライン インターフェイスを使用できます。 デプロイには、出力のモデル フォルダーを使用します。

    * [Azure Machine Learning スタジオを使用してデプロイする](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-azure-machine-learning-studio)
    * [Azure Machine Learning CLI (v2) を使用してデプロイする](how-to-deploy-mlflow-models-online-endpoints.md#deploy-using-cli-v2)。

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する](how-to-use-managed-online-endpoint-studio.md)
- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [マネージド オンライン エンドポイントを自動スケーリングする方法](how-to-autoscale-endpoints.md)
- [バッチ エンドポイント (プレビュー) を使用したバッチ スコアリング](how-to-use-batch-endpoint.md)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [マネージド オンライン エンドポイントとマネージド ID (プレビュー) を使用して Azure リソースにアクセスする](how-to-access-resources-from-endpoints-managed-identities.md)
- [マネージド オンライン エンドポイントのデプロイトをラブルシューティングする](how-to-troubleshoot-managed-online-endpoints.md)