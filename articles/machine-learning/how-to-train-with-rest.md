---
title: REST を使用してモデルをトレーニングする (プレビュー)
titleSuffix: Azure Machine Learning
description: REST API を使用してモデルをトレーニングし、ジョブを作成する方法について学習します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: wenxwei
ms.author: wenxwei
ms.date: 10/21/2021
ms.reviewer: peterlu
ms.custom: devplatv2
ms.openlocfilehash: f411d33baea7863b9139069f1eefd1282cf7b730
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131562088"
---
# <a name="train-models-with-rest-preview"></a>REST を使用してモデルをトレーニングする (プレビュー)

Azure Machine Learning REST API を使用してトレーニング ジョブを作成および管理する方法について学習します (プレビュー)。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

REST API では、標準の HTTP 動詞を使用して、リソースの作成、取得、更新、および削除を行います。 REST API は、HTTP 要求を作成できるすべての言語またはツールで使用できます。 REST は構造がわかりやすいため、スクリプト環境や MLOps オートメーションに適しています。

この記事では、新しい REST API を使用して次のことを行う方法について学習します。

> [!div class="checklist"]
> * 機械学習資産を作成する
> * 基本的なトレーニング ジョブを作成する 
> * ハイパーパラメーター調整スイープ ジョブを作成する

## <a name="prerequisites"></a>前提条件

- 管理者権限を持っている **Azure サブスクリプション**。 そのようなサブスクリプションがない場合は、[無料または有料の個人用サブスクリプション](https://azure.microsoft.com/free/)をお試しください。
- [Azure Machine Learning ワークスペース](how-to-manage-workspace.md)。
- ワークスペース内のサービス プリンシパル。 管理 REST 要求で[サービス プリンシパル認証](how-to-setup-authentication.md#use-service-principal-authentication)が使用されている。
- サービス プリンシパルの認証トークン。 「[サービス プリンシパルの認証トークンを取得する](./how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)」の手順に従って、このトークンを取得します。 
- **curl** ユーティリティ。 **curl** プログラムは、[Linux 用 Windows サブシステム](/windows/wsl/install-win10)または任意の UNIX ディストリビューションで使用できます。 PowerShell では、**curl** は **Invoke-WebRequest** の別名であり、`curl -d "key=val" -X POST uri` は `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri` になります。 

## <a name="azure-machine-learning-jobs"></a>Azure Machine Learning ジョブ
ジョブは、計算ジョブのすべての側面を指定するリソースです。 これには 3 つのものが集約されます。

- 実行対象
- 実行方法
- 実行する場所

SDK、Azure CLI、スタジオによる視覚的な方法など、Azure Machine Learning ジョブを送信する方法はたくさんあります。 次の例では、REST API を使用して LightGBM トレーニング ジョブを送信します。

## <a name="create-machine-learning-assets"></a>機械学習資産を作成する

最初に、Azure Machine Learning 資産を設定して、ジョブを構成します。

次の REST API 呼び出しでは、プレースホルダーとして `$SUBSCRIPTION_ID`、`$RESOURCE_GROUP`、`$LOCATION`、および `$WORKSPACE` を使用しています。 プレースホルダーを実際の値に置き換えてください。 

管理 REST により、[サービス プリンシパル認証トークン](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)が要求されます。 `$TOKEN` は、実際の値に置き換えてください。 このトークンは、次のコマンドを使用して取得できます。

```bash
TOKEN=$(az account get-access-token --query accessToken -o tsv)
```

サービス プロバイダーは、`api-version` 引数を使用して互換性を保証します。 `api-version` 引数はサービスによって異なります。 最新の Azure Machine Learning API のバージョンは `2021-03-01-preview` です。 将来のバージョンに対応するために、API バージョンを変数として設定します。

```bash
API_VERSION="2021-03-01-preview"
```

### <a name="compute"></a>Compute

機械学習ジョブを実行するには、コンピューティング リソースが必要です。 自分のワークスペースのコンピューティング リソースを一覧表示できます。

```bash
curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/computes?api-version=$API_VERSION" \
--header "Authorization: Bearer $TOKEN"
```

この例では、`cpu-cluster` という名前の既存のコンピューティング クラスターを使用します。 カプセル化のためにコンピューティング名を変数として設定します。

```bash
COMPUTE_NAME="cpu-cluster"
```

> [!TIP]
> [名前付きコンピューティング リソースは、PUT 要求を使用して作成または上書き](./how-to-manage-rest.md#create-and-modify-resources-using-put-and-post-requests)できます。 

### <a name="environment"></a>環境 

LightGBM の例は、LightGBM 環境で実行する必要があります。 PUT 要求を使用して環境を作成します。 Microsoft Container Registry の Docker イメージを使用します。

`Docker` を使用して Docker イメージを構成し、`condaFile` を使用して conda の依存関係を追加できます。 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_environment":::

### <a name="datastore"></a>データストア

トレーニング ジョブはデータに対して実行する必要があります。そのため、データストアを指定する必要があります。 この例では、自分のワークスペースの既定のデータストアと Azure ストレージ アカウントを取得します。 GET 要求を使用して自分のワークスペースに対してクエリを実行し、情報が含まれた JSON ファイルを取得します。

[jq](https://stedolan.github.io/jq/) ツールを使用して、JSON の結果を解析し、必要な値を取得できます。 Azure portal を使用して同じ情報を取得することもできます。

```bash
response=$(curl --location --request GET "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.MachineLearningServices/workspaces/$WORKSPACE/datastores?api-version=$API_VERSION&isDefault=true" \
--header "Authorization: Bearer $TOKEN")

AZURE_STORAGE_ACCOUNT=$(echo $response | jq '.value[0].properties.contents.accountName')
AZUREML_DEFAULT_DATASTORE=$(echo $response | jq '.value[0].name')
AZUREML_DEFAULT_CONTAINER=$(echo $response | jq '.value[0].properties.contents.containerName')
AZURE_STORAGE_KEY=$(az storage account keys list --account-name $AZURE_STORAGE_ACCOUNT | jq '.[0].value')
```

### <a name="data"></a>Data

データストアを用意できたら、データセットを作成できます。 この例では、共通のデータセット `iris.csv` を使用し、`path` でそれをポイントします。 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_data":::

### <a name="code"></a>コード

データセットとデータストアを用意できたら、ジョブで実行するトレーニング スクリプトをアップロードできます。 Azure Storage CLI を使用して、BLOB を既定のコンテナーにアップロードします。 また、Azure portal や Azure Storage Explorer など、他の方法でアップロードすることもできます。


```bash
az storage blob upload-batch -d $AZUREML_DEFAULT_CONTAINER/src \
 -s jobs/train/lightgbm/iris/src --account-name $AZURE_STORAGE_ACCOUNT --account-key $AZURE_STORAGE_KEY
```

コードをアップロードしたら、PUT 要求を使用してコードを指定し、`datastoreId` を使用してデータストアを参照することができます。 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_code":::

## <a name="submit-a-training-job"></a>トレーニング ジョブの送信

資産が準備できたので、トレーニングされたモデルとメタデータを出力する LightGBM ジョブを実行できます。 トレーニング ジョブを構成するには、次の情報が必要です。 

- **run_id**: [省略可能] ジョブの名前。これは、すべてのジョブで一意である必要があります。 YAML ファイル (`name` フィールド) またはコマンド ライン (`--name/-n`) に名前を指定しない限り、GUID または UUID が自動的に生成され、名前に使用されます。
- **jobType**: ジョブの種類。 基本的なトレーニング ジョブの場合は、`Command` を使用します。
- **codeId**: トレーニング スクリプトへのパス。
- **command**: 実行するコマンド。 入力データは、コマンドに書き込むことができ、データ バインディングを使用して参照できます。 
- **environmentId**: 自分の環境へのパス。
- **inputDataBindings**: データ バインディングは、入力データを参照するのに役立ちます。 環境変数を作成すると、バインディングの名前が AZURE_ML_INPUT_ に追加されます。これは、`command` で参照できます。 データ バインディングを作成するには、作成したデータへのパスを `dataId` として追加する必要があります。 
- **experimentName**: [省略可能] Azure Machine Learning スタジオでジョブを整理しやすいように、ジョブにタグを付けます。 各ジョブの実行レコードは、スタジオの [実験] タブの対応する実験の下に整理されます。省略した場合、タグは既定でジョブの作成時の作業ディレクトリの名前に設定されます。
- **compute**: `target` は、コンピューティング先を指定します。これには、自分のコンピューティングへのパスを設定できます。 `instanceCount` は、ジョブに必要なインスタンスの数を指定します。

次のコマンドを使用して、トレーニング ジョブを送信します。

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_job":::

## <a name="submit-a-hyperparameter-sweep-job"></a>ハイパーパラメーター スイープ ジョブを送信する

Azure Machine Learning では、トレーニング ハイパーパラメーターを効率的に調整することもできます。 REST API を使用して、ハイパーパラメーター調整スイートを作成できます。 Azure Machine Learning のハイパーパラメーター調整オプションの詳細については、[モデルのハイパーパラメーター調整](how-to-tune-hyperparameters.md)に関するページを参照してください。 ハイパーパラメーター調整パラメーターを指定して、スイープを構成します。

- **jobType**: ジョブの種類。 スイープ ジョブの場合、`Sweep` になります。 
- **algorithm:** : 手始めにサンプリング アルゴリズムの "random" から始めるのが良いでしょう。 オプションの列挙については、スイープ ジョブの[スキーマ](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)を参照してください。 
- **trial**: 実行する各トライアルのコマンド ジョブ構成。 
- **objective**: `primaryMetric` は最適化メトリックです。これは、トレーニング コードからログに記録されたメトリックの名前と一致する必要があります。 `goal` は、方向 (最小化または最大化) を指定します。 オプションの完全な列挙については、[スキーマ](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)を参照してください。 
- **searchSpace**: スイープするハイパーパラメーターの辞書。 キーは、ハイパーパラメーターの名前です (たとえば、`learning_rate`)。 値は、ハイパーパラメーター分布です。 オプションの列挙については、[スキーマ](https://azuremlschemas.azureedge.net/latest/sweepJob.schema.json)を参照してください。
- **maxTotalTrials**: 実行する個々のトライアルの最大数。
- **maxConcurrentTrials**: [省略可能] 自分のコンピューティング クラスターで同時に実行するトライアルの最大数。
- **timeout**: [省略可能] スイープ ジョブを実行する最大時間 (分)。

同じ LightGBM の例でスイープ ジョブを作成するには、次のコマンドを使用します。 

:::code language="rest-api" source="~/azureml-examples-main/cli/train-rest.sh" id="create_a_sweep_job":::

## <a name="next-steps"></a>次の手順

トレーニング済みのモデルを用意できたら、[モデルをデプロイする方法](how-to-deploy-and-where.md)について学習します。
