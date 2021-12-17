---
title: バッチ エンドポイントを使用したバッチ スコアリング
titleSuffix: Azure Machine Learning
description: この記事では、大きなデータのバッチ スコアリングを継続的に行うバッチ エンドポイントの作成方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 10/21/2021
ms.custom: how-to, devplatv2
ms.openlocfilehash: 481227a747fca327f107049734a69008aa3c3bcf
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063781"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>バッチ エンドポイント (プレビュー) を使用したバッチ スコアリング

バッチ エンドポイント (プレビュー) を使用してバッチ スコアリングを行う方法について説明します。 バッチ エンドポイントを使用すると、バッチ スコアリングのモデルをホストするプロセスが単純化され、インフラストラクチャに気を取られることなく、機械学習に専念することができます。 詳細については、「[Azure Machine Learning エンドポイント (プレビュー) とは](concept-endpoints.md)」を参照してください。

この記事では、次のタスクを行う方法について説明します。

> [!div class="checklist"]
> * バッチ エンドポイントと既定のバッチ デプロイを作成する
> * Azure CLI を使用してバッチ スコアリング ジョブを開始する
> * バッチ スコアリング ジョブの実行の進行状況を監視し、スコアリング結果を確認する
> * 既存のフローに影響を与えることなく、自動生成されたコードと環境を使用して既存のエンドポイントに新しい MLflow モデルをデプロイする
> * 新しいデプロイをテストし、既定のデプロイとして設定する
> * 使用されていないエンドポイントとデプロイを削除する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>必須コンポーネント

* Azure Machine Learning を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。

* Azure CLI と `ml` 拡張機能をインストールします。 [CLI (v2) (プレビュー) のインストール、設定、および使用](how-to-configure-cli.md)に関するページにあるインストール手順に従います。

* Azure リソース グループがない場合は作成します。自分 (または使用するサービス プリンシパル) に `Contributor` アクセス許可が割り当てられている必要があります。 リソース グループの作成については、[CLI (v2) (プレビュー) のインストール、設定、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure Machine Learning ワークスペースを作成します (まだない場合)。 ワークスペースの作成については、[CLI (v2) (プレビュー) のインストール、設定、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure CLI の既定のワークスペースとリソース グループを構成します。 Machine Learning CLI コマンドには、`--workspace/-w` および `--resource-group/-g` パラメーターが必要です。 既定値を構成すると、値を複数回渡すことを回避できます。 これらは、コマンド ラインでオーバーライドできます。 次のコードを実行して既定値を設定します。 詳細については、[CLI (v2) (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。

```azurecli
az account set -s "<subscription ID>"
az configure --defaults group="<resource group>" workspace="<workspace name>" location="<location>"
```

### <a name="clone-the-example-repository"></a>サンプル リポジトリをクローンする

次のコマンドを実行して [AzureML サンプル リポジトリ](https://github.com/Azure/azureml-examples)をクローンし、`cli` ディレクトリにアクセスします。 この記事では、`/cli/endpoints/batch` のアセットを使用します。エンド ツー エンドの動作例は `/cli/batch-score.sh` です。

```azurecli
git clone https://github.com/Azure/azureml-examples 
cd azureml-examples/cli
```

エンドポイント名を設定します。 `YOUR_ENDPOINT_NAME` を Azure リージョン内の一意の名前に置き換えます。

Unix の場合、次のコマンドを実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="set_variables" :::

Windows の場合、次のコマンドを実行します。

```azurecli
set ENDPOINT_NAME="<YOUR_ENDPOINT_NAME>"
```

> [!NOTE]
> バッチ エンドポイント名は Azure リージョン内で一意である必要があります。 たとえば、westus2 に mybatchendpoint という名前のバッチ エンドポイントは 1 つしか存在できません。

### <a name="create-compute"></a>コンピューティングを作成する

バッチ エンドポイントは、クラウド コンピューティング リソースでのみ動作し、ローカルでは動作しません。 クラウド コンピューティング リソースは再利用可能な仮想コンピューター クラスターです。 次のコードを実行して、Azure Machine Learning コンピューティング クラスターを作成します。 この記事の以降の例では、ここで作成した `batch-cluster` という名前のコンピューティングを使用しています。 必要に応じて調整し、`azureml:<your-compute-name>` を使用して自分のコンピューティングを参照します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_compute" :::

> [!NOTE]
> この時点では、コンピューティングに課金はされません。バッチ エンドポイントが呼び出されてバッチ スコアリング ジョブが送信されるまで、クラスターは 0 ノードのままだからです。 [AmlCompute のコストの管理および最適化](how-to-manage-optimize-cost.md#use-azure-machine-learning-compute-cluster-amlcompute)について確認してください。

## <a name="understand-batch-endpoints-and-batch-deployments"></a>バッチ エンドポイントとバッチ デプロイについて

バッチ エンドポイントは、クライアントがバッチ スコアリング ジョブをトリガーするために呼び出すことができる HTTPS エンドポイントです。 バッチ スコアリング ジョブは、複数の入力をスコアリングするジョブです (詳細については、「[バッチ エンドポイントとは](concept-endpoints.md#what-are-batch-endpoints-preview)」を参照してください)。 バッチ デプロイは、実際のバッチ スコアリングを行うモデルをホストするコンピューティング リソースのセットです。 1 つのバッチ エンドポイントに複数のバッチ デプロイを割り当てることができます。 

> [!TIP]
> その複数のバッチ デプロイのうちの 1 つが、エンドポイントの既定のデプロイとして機能します。 既定のデプロイは、エンドポイントが呼び出されたときに実際のバッチ スコアリングを行うために使用されます。 バッチ エンドポイントとバッチ デプロイの詳細については、[こちら](concept-endpoints.md#what-are-batch-endpoints-preview)を参照してください。

次の YAML ファイルは、バッチ エンドポイントを定義しています。それを[バッチ エンドポイント作成](#create-a-batch-endpoint)のための CLI コマンドに含めることができます。 リポジトリでは、このファイルは `/cli/endpoints/batch/batch-endpoint.yml` にあります。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/batch-endpoint.yml":::

次の表では、エンドポイント YAML の主要なプロパティについて説明します。 完全なバッチ エンドポイントの YAML スキーマについては、「[CLI (v2) バッチ エンドポイントの YAML スキーマ](./reference-yaml-endpoint-batch.md)」を参照してください。

| Key | 説明 |
| --- | ----------- |
| `$schema` | [省略可能] YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、バッチ エンドポイントの YAML ファイルで使用可能なすべてのオプションを確認できます。 |
| `name` | バッチ エンドポイントの名前。 Azure リージョン レベルで一意である必要があります。|
| `auth_mode` | バッチ エンドポイントの認証方法。 現在、Azure Active Directory トークンベースの認証 (`aad_token`) のみがサポートされています。 |
| `defaults.deployment_name` | エンドポイントの既定のデプロイとして使用されるデプロイの名前。 |

バッチ デプロイを作成するには、次のすべての項目が必要です。
* モデル ファイル、または自分のワークスペース内の登録済みモデル (`azureml:<model-name>:<model-version>` を使用して参照する)。 
* モデルをスコアリングするコード。
* モデルを実行する環境。 これは、Conda の依存関係がある Docker イメージ、または自分のワークスペースに既に登録済みの環境 (`azureml:<environment-name>:<environment-version>` を使用して参照する) とすることができます。
* 事前作成済みコンピューティング (`azureml:<compute-name>` を使用して参照する) とリソース設定。

Azure ML エンティティを参照する方法の詳細については、「[Azure ML エンティティの参照](reference-yaml-core-syntax.md#referencing-an-azure-ml-entity)」を参照してください。

サンプル リポジトリには、必要なすべてのファイルが含まれています。 次の YAML ファイルでは、必要なすべての入力とオプションの設定を使用してバッチ デプロイを定義しています。 このファイルを CLI コマンドに含めて、[バッチ デプロイを作成する](#create-a-batch-deployment)ことができます。 リポジトリでは、このファイルは `/cli/endpoints/batch/nonmlflow-deployment.yml` にあります。 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/nonmlflow-deployment.yml":::

次の表では、デプロイ YAML の主要なプロパティについて説明します。 完全なバッチ デプロイの YAML スキーマについては、「[CLI (v2) バッチ デプロイの YAML スキーマ](./reference-yaml-deployment-batch.md)」を参照してください。

| Key | 説明 |
| --- | ----------- |
| `$schema` | [省略可能] YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、バッチ デプロイの YAML ファイルで使用可能なすべてのオプションを確認できます。 |
| `name` | デプロイの名前。 |
| `endpoint_name` | デプロイを作成するエンドポイントの名前。 |
| `model` | バッチ スコアリングに使用するモデル。 この例では、`local_path` を使用してインラインでモデルを定義しています。 モデル ファイルは自動的にアップロードされ、自動生成された名前とバージョンで登録されます。 その他のオプションについては、[モデル スキーマ](reference-yaml-model.md#yaml-syntax)に従ってください。 運用シナリオのベスト プラクティスとして、モデルを個別に作成し、ここで参照するようにしてください。 既存のモデルを参照するには、`azureml:<model-name>:<model-version>` 構文を使用します。 |
| `code_configuration.code.local_path` | モデルをスコアリングするためのすべての Python ソース コードを格納するディレクトリ。 |
| `code_configuration.scoring_script` | 上記のディレクトリ内の Python ファイル。 このファイルには、`init()` 関数と `run()` 関数が記述されている必要があります。 `init()` 関数は、コストのかかる、または一般的な準備を行うときに使用します (たとえば、メモリにモデルを読み込むなど)。 `init()` は、プロセスの開始時に 1 回だけ呼び出されます。 各エントリをスコアリングするには、`run(mini_batch)` を使用します。`mini_batch` の値は、ファイル パスのリストです。 `run()` 関数からは、Pandas の DataFrame または配列が返されます。 返された要素はそれぞれ、`mini_batch` 内で成功した入力要素の 1 回の実行を示します。 入力を出力と関連付けるのに十分なデータが `run()` 応答に含まれている必要があります。 |
| `environment` | モデルをスコアリングする環境。 この例では、`conda_file` と `image` を使用して環境をインラインで定義しています。 `image` 上に `conda_file` の依存関係がインストールされます。 環境は、自動生成された名前とバージョンで自動的に登録されます。 その他のオプションについては、[環境スキーマ](reference-yaml-environment.md#yaml-syntax)に従ってください。 運用シナリオのベスト プラクティスとして、環境を別途作成し、ここで参照することをお勧めします。 既存の環境を参照するには、`azureml:<environment-name>:<environment-version>` 構文を使用します。 |
| `compute` | バッチ スコアリングを実行するコンピューティング。 この例では、最初に作成した `batch-cluster` を使用し、`azureml:<compute-name>` を使用してそれを参照します。 |
| `resources.instance_count` | 各バッチ スコアリング ジョブに使用されるインスタンスの数。 |
| `max_concurrency_per_instance` | [省略可能] インスタンスあたりの `scoring_script` の並列実行の最大数。 |
| `mini_batch_size` | [省略可能] `scoring_script` が 1 回の `run()` 呼び出しで処理できるファイルの数。 |
| `output_action` | [省略可能] 出力ファイルで出力を整理する方法。 `append_row` を使用すると、`run()` で返されたすべての出力結果が、`output_file_name` という名前の 1 つのファイルにマージされます。 `summary_only` を使用すると、出力結果はマージされず、`error_threshold` の計算だけが行われます。 |
| `output_file_name` | [省略可能] `append_row` `output_action` のバッチ スコアリング出力ファイルの名前。 |
| `retry_settings.max_retries` | [省略可能] `scoring_script` `run()` が失敗した場合の最大再試行回数。 |
| `retry_settings.timeout` | [省略可能] ミニ バッチをスコアリングするための `scoring_script` `run()` に適用するタイムアウト (秒単位)。 |
| `error_threshold` | [省略可能] 無視する必要がある入力ファイルのスコアリング エラーの数。 入力全体でのエラーの数がこの値を超えると、ジョブは終了します。 この例では `-1` を使用しています。これは、バッチ スコアリング ジョブを終了することなく、任意の数のエラーが許容されることを示します。 | 
| `logging_level` | [省略可能] ログの詳細度。 値は詳細度が低い順に WARNING、INFO、DEBUG です。 |

###  <a name="understand-the-scoring-script"></a>スコアリング スクリプトを理解する

前述のように、`code_configuration.scoring_script` には 2 つの関数が含まれている必要があります。

- `init()`: この関数は、コストのかかる、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。
-  `run(mini_batch)`: この関数は各 `mini_batch` に対して呼び出され、実際のスコアリングを行います。 
    -  `mini_batch`: `mini_batch` の値は、ファイル パスのリストです。
    -  `response`: `run()` メソッドは、Pandas の DataFrame または配列を返す必要があります。 返された出力要素はそれぞれ、入力 `mini_batch` 内で成功した入力要素の 1 回の実行を示します。 入力と出力結果を関連付けるのに十分なデータ (各入力要素の識別子など) が `run()` 応答に含まれている必要があります。 

この例では、`/cli/endpoints/batch/mnist/code/digit_identification.py` を使用します。 モデルは、`init()` 内で `AZUREML_MODEL_DIR` から読み込まれます。これはデプロイ時に作成されたモデル フォルダーのパスです。 `run(mini_batch)` により、`mini_batch` 内の各ファイルが反復処理され、実際のモデル スコアリングが行われて、出力結果が返されます。

## <a name="deploy-with-batch-endpoints-and-run-batch-scoring"></a>バッチ エンドポイントを使用してデプロイし、バッチ スコアリングを実行する

次に、バッチ エンドポイントを使用してモデルをデプロイし、バッチ スコアリングを実行しましょう。

### <a name="create-a-batch-endpoint"></a>バッチ エンドポイントを作成する

バッチ エンドポイントを作成する最も簡単な方法は、`--name` のみを提供する次のコードを実行することです。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_endpoint" :::

YAML ファイルを使用してバッチ エンドポイントを作成することもできます。 上記のコマンドに `--file` パラメーターを追加し、YAML ファイルのパスを指定します。

### <a name="create-a-batch-deployment"></a>バッチ デプロイを作成する

次のコードを実行して、バッチ エンドポイントに `nonmlflowdp` という名前のバッチ デプロイを作成し、それを既定のデプロイとして設定します。 

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_batch_deployment_set_default" :::

> [!TIP]
> `--set-default` は、新しく作成したデプロイをエンドポイントの既定のデプロイとして設定するパラメーターです。 これは、特に最初のデプロイを作成する場合に、エンドポイントの新しい既定のデプロイを作成する便利な方法です。 運用シナリオのベスト プラクティスとして、新しいデプロイを作成し、それを既定として設定せずに検証して、後で既定のデプロイを更新することをお勧めします。 詳細については、「[新しいモデルをデプロイする](#deploy-a-new-model)」セクションを参照してください。

### <a name="check-batch-endpoint-and-deployment-details"></a>バッチ エンドポイントとデプロイの詳細を確認する

エンドポイントとデプロイの詳細を確認するには、`show` を使用します。

バッチ デプロイを確認するには、次のコードを実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_deployment_detail" :::

バッチ エンドポイントを確認するには、次のコードを実行します。 新しく作成したデプロイを既定のデプロイとして設定してあるので、応答の `defaults.deployment_name` に `nonmlflowdp` が表示されます。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_batch_endpooint_detail" :::

### <a name="invoke-the-batch-endpoint-to-start-a-batch-scoring-job"></a>バッチ エンドポイントを呼び出してバッチ スコアリング ジョブを開始する

バッチ エンドポイントを呼び出すと、バッチ スコアリング ジョブがトリガーされます。 ジョブの `name` は呼び出しの応答から返され、バッチ スコアリングの進行状況を追跡するために使用できます。 バッチ スコアリング ジョブは、一定の期間実行されます。 入力全体が複数の `mini_batch` に分割され、コンピューティング クラスター上で並列処理が行われます。 1 つの `scoring_scrip` `run()` で 1 つの `mini_batch` を受け取り、インスタンス上のプロセスによってそれを処理します。 バッチ スコアリング ジョブの出力は、クラウド ストレージに格納されます。これはワークスペースの既定の BLOB ストレージ、または指定したストレージのいずれかです。

#### <a name="invoke-the-batch-endpoint-with-different-input-options"></a>異なる入力オプションを使用してバッチ エンドポイントを呼び出す

エンドポイントの `invoke` には CLI または REST のどちらでも使用できます。 REST エクスペリエンスについては、「[REST でのバッチ エンドポイント (プレビュー) の使用](how-to-deploy-batch-with-rest.md)」を参照してください

CLI の `invoke` でデータ入力を指定するには、3 つの方法があります。

* __方法 1: クラウド内のデータ__

    `--input-path` を使用して、Azure Machine Learning の登録済みデータストア内のフォルダー (プレフィックス `folder:` を使用) またはファイル (プレフィックス `file:` を使用) を指定します。 データ URI の構文は、フォルダーの場合は `folder:azureml://datastores/<datastore-name>/paths/<data-path>/`、特定のファイルの場合は `file:azureml://datastores/<datastore-name>/paths/<data-path>/<file-name>` です。 データ URI の詳細については、「[Azure Machine Learning のデータ参照 URI](reference-yaml-core-syntax.md#azure-ml-data-reference-uri)」を参照してください。

    この例では、`https://pipelinedata.blob.core.windows.net/sampledata/mnist` のフォルダー内に一般公開されているデータを使用します。これには、何千もの手書きの数字が含まれています。 バッチ スコアリング ジョブの名前は、呼び出し応答から返されます。 次のコードを実行し、このデータを使用してバッチ エンドポイントを呼び出します。 `--query name` は、呼び出し応答からジョブ名だけを返すために追加され、後で[バッチ スコアリング ジョブ実行の進行状況の監視](#monitor-batch-scoring-job-execution-progress)と[バッチ スコアリング結果の確認](#check-batch-scoring-results)に使用されます。 完全な呼び出し応答を表示する場合は、`--query name -o tsv` を削除します。 `--query` パラメーターの詳細については、「[Azure CLI コマンドの出力のクエリ](/cli/azure/query-azure-cli)」を参照してください。

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job" :::

* __方法 2: 登録済みデータセット__

    `--input-dataset` を使用して Azure Machine Learning の登録済みデータセットを渡します。 データセットを作成するには、`az ml dataset create -h` で手順を確認し、[データセット スキーマ](reference-yaml-dataset.md#yaml-syntax)に従います。

    > [!NOTE]
    > 前のバージョンの CLI と Python SDK を使用して作成された FileDataset も使用できます。 TabularDataset はサポートされていません。 

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-dataset azureml:<dataset-name>:<dataset-version>
    ```

* __方法 3: ローカルの格納データ__

    `--input-local-path` を使用して、ローカルに格納されているデータ ファイルを渡します。 データ ファイルは自動的にアップロードされ、自動生成された名前とバージョンで登録されます。

    ```azurecli
    az ml batch-endpoint invoke --name $ENDPOINT_NAME --input-local-path <local-path>
    ```

#### <a name="configure-the-output-location-and-overwrite-settings"></a>出力場所を構成して設定を上書きする

既定では、バッチ スコアリングの結果が、ワークスペースの既定の BLOB ストアに格納されます。格納先は、ジョブ名 (システムにより生成された GUID) に基づいて命名されたフォルダーです。 スコアリング出力の格納場所は、バッチ エンドポイントを呼び出すときに構成できます。 Azure Machine Learning の登録済みデータストア内の `folder:` を構成するには、`--output-path` を使用します。 `--output-path` `folder:` の構文は、`--input-path` `folder:` と同じです。 すべてのスコアリング結果を含む 1 つの出力ファイルを使用する場合は (デプロイ YAML の `output_action=append_row` で指定します)、`--set output_file_name=<your-file-name>` を使用して新しい出力ファイル名を構成します。

> [!IMPORTANT]
> 一意の出力場所を使用する必要があります。 出力ファイルが存在する場合、バッチ スコアリング ジョブでエラーが発生します。 

一部の設定を上書きすることで、コンピューティング リソースを有効活用し、パフォーマンスを高めることができます。 

* `instance_count` を上書きするには、`--instance-count` を使用します。 たとえば、データ入力の量が多い場合は、より多くのインスタンスを使用して、エンド ツー エンドのバッチ スコアリングを高速化することができます。
* `mini_batch_size` を上書きするには、`--mini-batch-size` を使用します。 ミニ バッチの数は、入力ファイルの合計数と mini_batch_size によって決まります。 mini_batch_size が小さければ、より多くのミニ バッチが生成されます。 ミニ バッチは並列で実行できますが、追加のスケジューリングと呼び出しのオーバーヘッドが発生する可能性があります。 
* その他の設定、たとえば `max_retries`、`timeout`、`error_threshold` は、`--set` を使用して上書きします。 これらの設定は、さまざまなワークロードを対象にしたエンド ツー エンドのバッチ スコアリング時間に影響を与える可能性があります。

出力場所を指定し、呼び出し時に設定を上書きするには、次のコードを実行します。 この例では、ワークスペースの既定の BLOB ストレージ内のエンドポイントと同じ名前のフォルダーに出力を格納し、また、ランダムなファイル名を使用して出力場所の一意性を確保しています。 このコードは Unix で動作します。 ご自身の一意のフォルダーおよびファイル名に置き換えてください。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="start_batch_scoring_job_configure_output_settings" :::

### <a name="monitor-batch-scoring-job-execution-progress"></a>バッチ スコアリング ジョブ実行の進行状況を監視する

バッチ スコアリング ジョブは、入力のセット全体を処理するために、ある程度時間がかかるのが普通です。 

CLI の `job show` を使用してジョブを表示できます。 次のコードを実行して、前回のエンドポイント呼び出し以降のジョブの状態を確認します。 ジョブ コマンドの詳細を確認するには、`az ml job -h` を実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="check_job_status" :::

### <a name="check-batch-scoring-results"></a>バッチ スコアリングの結果を確認する

ジョブが完了したときに Azure Storage Explorer でスコアリング結果を表示するには、次の手順に従います。

1. 次のコードを実行して、Azure Machine Learning スタジオでバッチ スコアリング ジョブを開きます。 ジョブのスタジオ リンクは、`invoke` の応答にも `interactionEndpoints.Studio.endpoint` の値として含まれています。

    :::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="show_job_in_studio" :::

1. 実行のグラフで、`batchscoring` ステップを選択します。
1. __[出力 + ログ]__ タブを選択し、 **[Show data outputs]\(データ出力の表示\)** を選択します。
1. __[Data outputs]\(データ出力\)__ で、__Storage Explorer__ を開くアイコンを選択します。

:::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="データ出力場所の表示を示すスタジオのスクリーンショット" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::

Storage Explorer のスコアリング結果は、次のサンプル ページのようになります。

:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.png" alt-text="スコアリング出力のスクリーンショット" lightbox="media/how-to-use-batch-endpoint/scoring-view.png":::

## <a name="deploy-a-new-model"></a>新しいモデルをデプロイする

バッチ エンドポイントを作成したら、引き続きモデルを改良して、新しいデプロイを追加できます。

### <a name="create-a-new-batch-deployment-hosting-an-mlflow-model"></a>MLflow モデルをホストする新しいバッチ デプロイを作成する

既存のバッチ エンドポイントに新しいバッチ デプロイを作成し、既定のデプロイとして設定しない場合は、次のコードを実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="create_new_deployment_not_default" :::

`--set-default` は使用されないことに注意してください。 バッチ エンドポイントの `show` を再度実行すると、`defaults.deployment_name` に変更がないことがわかります。 

この例では、MLflow でトレーニングおよび追跡されているモデル (`/cli/endpoints/batch/autolog_nyc_taxi`) を使用します。 `scoring_script` と `environment` は、モデルのメタデータを使用して自動生成できます。YAML ファイルで指定する必要はありません。 MLflow の詳細については、[MLflow と Azure Machine Learning を使用した ML モデルのトレーニングと追跡 (プレビュー)](how-to-use-mlflow.md) に関するページを参照してください。

MLflow モデルをデプロイするために例で使用している YAML ファイルを次に示します。これには、最低限必要なプロパティだけが含まれています。 リポジトリ内のソース ファイルは `/cli/endpoints/batch/mlflow-deployment.yml` です。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/mlflow-deployment.yml":::

> [!NOTE]
> `scoring_script` と `environment` の自動生成でサポートされているのは、Python 関数モデルのフレーバーと列ベースのモデル シグネチャだけです。

### <a name="test-a-non-default-batch-deployment"></a>既定以外のバッチ デプロイをテストする

新しい既定以外のデプロイをテストするには、次のコードを実行します。 この例では、一般公開されている csv ファイル (`https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv`) を受け入れる別のモデルを使用しています。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="test_new_deployment" :::

新しいデプロイ名を指定するために `--deployment-name` が使用されていることに注意してください。 このパラメーターを使用すると、既定以外のデプロイを `invoke` で呼び出すことができます。バッチ エンドポイントの既定のデプロイは更新されません。

### <a name="update-the-default-batch-deployment"></a>既定のバッチ デプロイを更新する

エンドポイントの既定のバッチ デプロイを更新するには、次のコードを実行します。

:::code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="update_default_deployment" :::

バッチ エンドポイントを再度 `show` で表示すると、今度は `defaults.deployment_name` が `mlflowdp` に設定されていることがわかります。 `--deployment-name` パラメーターを使用せずに、バッチ エンドポイントを `invoke` で直接呼び出すことができます。

### <a name="optional-update-the-deployment"></a>(省略可能) デプロイを更新する

デプロイを更新する (たとえば、コード、モデル、環境、または設定を更新する) 場合は、YAML ファイルを更新してから、`az ml batch-deployment update` を実行します。 YAML ファイルを使用せずに `--set` を使用して更新することもできます。 詳細については `az ml batch-deployment update -h` を確認してください。

## <a name="delete-the-batch-endpoint-and-the-deployment"></a>エンドポイントとデプロイを削除する

古いバッチ デプロイを今後使用しない場合は、次のコードを実行して削除する必要があります。 `--yes` は、削除を確認するために使用します。

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_deployment" :::

次のコードを実行して、バッチ エンドポイントと基になるすべてのデプロイを削除します。 バッチ スコアリング ジョブは削除されません。

::: code language="azurecli" source="~/azureml-examples-main/cli/batch-score.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>次の手順

* [スタジオのバッチ エンドポイント](how-to-use-batch-endpoints-studio.md)
* [バッチ スコアリングのために REST (プレビュー) を使用してモデルをデプロイする](how-to-deploy-batch-with-rest.md)
* [バッチ エンドポイントのトラブルシューティング](how-to-troubleshoot-batch-endpoints.md)
