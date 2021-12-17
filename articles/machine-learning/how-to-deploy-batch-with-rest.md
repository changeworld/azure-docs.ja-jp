---
title: REST API でバッチ エンドポイントを使用してモデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: REST API でバッチ エンドポイントを使用してモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: tracych
ms.author: tracych
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 351855c870f3f3658488c66c401cef1c3c4b17c4
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059811"
---
# <a name="deploy-models-with-rest-preview-for-batch-scoring"></a>REST を使用してバッチ スコアリング用のモデルをデプロイする (プレビュー) 

Azure Machine Learning REST API を使用してバッチ スコアリング用のモデルをデプロイする方法について学習します (プレビュー)。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

REST API では、標準の HTTP 動詞を使用して、リソースの作成、取得、更新、および削除を行います。 REST API は、HTTP 要求を作成できるすべての言語またはツールで使用できます。 REST は構造がわかりやすいため、スクリプト環境や MLOps オートメーションに適しています。

この記事では、新しい REST API を使用して次のことを行う方法について学習します。

> [!div class="checklist"]
> * 機械学習資産を作成する
> * バッチ エンドポイントとバッチ デプロイを作成する
> * バッチ エンドポイントを呼び出してバッチ スコアリング ジョブを開始する

## <a name="prerequisites"></a>前提条件

- 管理者権限を持っている **Azure サブスクリプション**。 そのようなサブスクリプションがない場合は、[無料または有料の個人用サブスクリプション](https://azure.microsoft.com/free/)をお試しください。
- [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。
- ワークスペース内のサービス プリンシパル。 管理 REST 要求で[サービス プリンシパル認証](how-to-setup-authentication.md#use-service-principal-authentication)が使用されている。
- サービス プリンシパルの認証トークン。 「[サービス プリンシパルの認証トークンを取得する](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)」の手順に従って、このトークンを取得します。 
- **curl** ユーティリティ。 **curl** プログラムは、[Linux 用 Windows サブシステム](/windows/wsl/install-win10)または任意の UNIX ディストリビューションで使用できます。 PowerShell では、**curl** は **Invoke-WebRequest** の別名であり、`curl -d "key=val" -X POST uri` は `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` になります。 
- [jq](https://stedolan.github.io/jq/) JSON プロセッサ。

> [!IMPORTANT]
> この記事のコード スニペットでは、お客様が Bash シェルを使用していることを前提とします。
>
> コード スニペットは、[Azure ML サンプル リポジトリ](https://github.com/Azure/azureml-examples)内の `/cli/batch-score-rest.sh` ファイルから取得されます。

## <a name="set-endpoint-name"></a>エンドポイント名を設定する

> [!NOTE]
> バッチ エンドポイントの名前は、Azure リージョン レベルで一意である必要があります。 たとえば、westus2 に存在することができる、mybatchendpoint という名前のバッチ エンドポイントは 1 つだけです。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-batch-endpoints"></a>Azure Machine Learning バッチ エンドポイント

[バッチ エンドポイント (プレビュー)](concept-endpoints.md#what-are-batch-endpoints-preview) を使用すると、バッチ スコアリングのモデルをホストするプロセスが単純化され、インフラストラクチャに気を取られることなく、機械学習に専念することができます。 この記事では、バッチ エンドポイントおよびデプロイを作成し、それを呼び出してバッチ スコアリング ジョブを開始します。 ただし、まず、モデル、コード、環境など、デプロイに必要な資産を登録する必要があります。

Azure Machine Learning バッチ エンドポイントを作成するには、[Azure CLI を含め](how-to-use-batch-endpoint.md)、さまざまな方法があります。[Studio](how-to-use-batch-endpoints-studio.md) を使用して視覚的に作成することもできます。 次の例では、REST API を使用してバッチ エンドポイントおよびデプロイを作成します。

## <a name="create-machine-learning-assets"></a>機械学習資産を作成する

最初に、Azure Machine Learning 資産を設定して、ジョブを構成します。

次の REST API 呼び出しでは、プレースホルダーとして `SUBSCRIPTION_ID`、`RESOURCE_GROUP`、`LOCATION`、および `WORKSPACE` を使用しています。 プレースホルダーを実際の値に置き換えてください。 

管理 REST により、[サービス プリンシパル認証トークン](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)が要求されます。 `TOKEN` は、実際の値に置き換えてください。 このトークンは、次のコマンドを使用して取得できます。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="13":::

サービス プロバイダーは、`api-version` 引数を使用して互換性を保証します。 `api-version` 引数はサービスによって異なります。 将来のバージョンに対応するために、API バージョンを変数として設定します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" range="11":::

### <a name="create-compute"></a>コンピューティングを作成する
バッチ スコアリングは、クラウド コンピューティング リソースでのみ動作し、ローカルでは動作しません。 クラウド コンピューティング リソースとは、バッチ スコアリング ワークフローを実行できる再利用可能な仮想コンピューター クラスターです。

次のようにコンピューティング クラスターを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_compute":::

> [!TIP]
> 代わりに既存のコンピューティングを使用する場合は、[バッチ デプロイの作成](#create-batch-deployment)時に、完全な Azure Resource Manager ID を指定する必要があります。 完全な ID には、形式 `/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes/<your-compute-name>` が使用されます。

### <a name="get-storage-account-details"></a>ストレージ アカウントの詳細を取得する

モデルとコードを登録するには、まず、それらをストレージ アカウントにアップロードする必要があります。 ストレージ アカウントの詳細は、データ ストアで使用できます。 この例では、自分のワークスペースの既定のデータストアと Azure ストレージ アカウントを取得します。 GET 要求を使用して自分のワークスペースに対してクエリを実行し、情報が含まれた JSON ファイルを取得します。

[jq](https://stedolan.github.io/jq/) ツールを使用して、JSON の結果を解析し、必要な値を取得できます。 Azure portal を使用しても同じ情報を取得することができます。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>コードをアップロードして登録する

データストアを用意できたら、スコアリング スクリプトをアップロードできます。 Azure Storage CLI を使用して、BLOB を既定のコンテナーにアップロードします。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_code":::

> [!TIP]
> また、Azure portal や [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など、他の方法でアップロードすることもできます。

自分のコードをアップロードしたら、PUT 要求を使用してそのコードを指定できます。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>モデルをアップロードして登録する

コードと同様に、モデル ファイルをアップロードします。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="upload_model":::

モデルを登録します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_model":::

### <a name="create-environment"></a>環境を作成する
デプロイは、必要な依存関係がある環境で実行する必要があります。 PUT 要求を使用して環境を作成します。 Microsoft Container Registry の Docker イメージを使用します。 `image` を使用して Docker イメージを構成し、`condaFile` を使用して conda の依存関係を追加できます。

次のコードを実行して、json で定義されている `condaFile` を読み取ります。 ソース ファイルは、サンプル リポジトリの `/cli/endpoints/batch/mnist/environment/conda.json` にあります。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="read_condafile":::

ここで、次のスニペットを実行して環境を作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_environment":::

## <a name="deploy-with-batch-endpoints"></a>バッチ エンドポイントを使用してデプロイする

次に、バッチ エンドポイントおよびデプロイを作成し、既定のデプロイを設定します。

### <a name="create-batch-endpoint"></a>バッチ エンドポイントを作成する

次のようにバッチ エンドポイントを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_endpoint":::

### <a name="create-batch-deployment"></a>バッチ デプロイを作成する

次のようにエンドポイントの下にバッチ デプロイを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_deployment":::

### <a name="set-the-default-batch-deployment-under-the-endpoint"></a>エンドポイントの下に既定のバッチ デプロイを設定する

1 つのエンドポイントの下に既定のバッチ デプロイは 1 つだけあります。これは、バッチス コアリング ジョブを実行するための呼び出し時に使用されます。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="set_endpoint_defaults":::

## <a name="run-batch-scoring"></a>バッチ スコアリングを実行する

バッチ エンドポイントを呼び出すと、バッチ スコアリング ジョブがトリガーされます。 ジョブ `id` が応答で返されます。それを使用してバッチ スコアリングの進行状況を追跡できます。 次のスニペットでは、ジョブ `id` を取得するために `jq` が使用されています。

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>バッチ エンドポイントを呼び出してバッチ スコアリング ジョブを開始する

バッチ エンドポイントを呼び出すには、スコアリング URI とアクセス トークンを取得します。 最初にスコアリング URI を取得します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_endpoint":::

バッチ エンドポイントのアクセス トークンを取得します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="get_access_token":::

次に、バッチ エンドポイントを呼び出して、バッチ スコアリング ジョブを開始します。 次の例では、クラウドで一般公開されているデータにスコア付けします。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_data_in_cloud":::

ご利用のデータが、Azure Machine Learning に登録されたデータ ストアに保存されている場合は、データセットを使用してバッチ エンドポイントを呼び出すことができます。 次のコードでは、新しいデータセットを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="create_dataset":::

次に、バッチ エンドポイントの呼び出し時にそのデータセットを参照します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="score_endpoint_with_dataset":::

前のコード スニペットで、カスタム出力の場所は `datastoreId`、`outputFileName`、および `path` を使用して指定されています。 これらの設定を使用すると、バッチ スコアリング結果を格納する場所を構成できます。

> [!IMPORTANT]
> 一意の出力場所を指定する必要があります。 出力ファイルが既に存在する場合、バッチ スコアリング ジョブは失敗します。

この例では、出力はワークスペースの既定の BLOB ストレージに格納されます。 フォルダー名はエンドポイント名と同じであり、ファイル名は次のコードによってランダムに生成されます。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score-rest.sh" ID="unique_output" :::

### <a name="check-the-batch-scoring-job"></a>バッチ スコアリング ジョブを確認する

バッチ スコアリング ジョブは、入力のセット全体を処理するために、ある程度時間がかかるのが普通です。 ジョブの状態を監視し、それが完了した後に結果を確認します。

> [!TIP]
> この例では、バッチ エンドポイントの既定のデプロイが呼び出されます。 既定以外のデプロイを呼び出すには、`azureml-model-deployment` HTTP ヘッダーを使用し、値をデプロイ名に設定します。 たとえば、curl で `--header "azureml-model-deployment: $DEPLOYMENT_NAME"` というパラメーターを使用します。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="check_job":::

### <a name="check-batch-scoring-results"></a>バッチ スコアリングの結果を確認する

結果の確認については、「[バッチ スコアリングの結果を確認する](how-to-use-batch-endpoint.md#check-batch-scoring-results)」を参照してください。

## <a name="delete-the-batch-endpoint"></a>バッチ エンドポイントを削除する

今後使用する予定がない場合、以下のコマンドでバッチ エンドポイントを削除してください (バッチ エンドポイントとそこにあるすべてのデプロイが削除されます)。

:::code language="rest-api" source="~/azureml-examples-main/cli/batch-score-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>次の手順

* [Azure CLI を使用](how-to-use-batch-endpoint.md)してバッチ スコアリング用のモデルをデプロイする方法について説明します。
* [Studio を使用](how-to-use-batch-endpoints-studio.md)してバッチ スコアリング用のモデルをデプロイする方法について説明します。
* [バッチ エンドポイントのトラブルシューティング](how-to-troubleshoot-batch-endpoints.md)について説明します
