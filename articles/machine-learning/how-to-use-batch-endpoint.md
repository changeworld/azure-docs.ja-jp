---
title: バッチ エンドポイントを使用したバッチ スコアリング
titleSuffix: Azure Machine Learning
description: この記事では、大きなデータのバッチ スコアリングを継続的に行うバッチ エンドポイントの作成方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: tracych
ms.author: tracych
ms.reviewer: laobri
ms.date: 5/25/2021
ms.custom: how-to
ms.openlocfilehash: 53fa68fdffd27c1d48322104c541894c6f9c4dd8
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751255"
---
# <a name="use-batch-endpoints-preview-for-batch-scoring"></a>バッチ エンドポイント (プレビュー) を使用したバッチ スコアリング

この記事では、バッチ エンドポイント (プレビュー) を使用してバッチ スコアリングを実行する方法について説明します。 バッチ エンドポイントを使用すると、バッチ スコアリングのモデルをホストするプロセスが単純化され、インフラストラクチャに気を取られることなく、機械学習に専念することができます。 バッチ エンドポイントを作成したら、Azure CLI から、または HTTP ライブラリと REST API を使用したあらゆるプラットフォームから、バッチ スコアリング ジョブをトリガーできます。 詳細については、「[Azure Machine Learning エンドポイント (プレビュー) とは](concept-endpoints.md)」を参照してください。

この記事では、次のタスクを行う方法について説明します。

> [!div class="checklist"]
> * MLflow モデルのバッチ エンドポイントをノー コード エクスペリエンスで作成する
> * バッチ エンドポイントの詳細を確認する
> * CLI を使用してバッチ スコアリング ジョブを開始する
> * バッチ スコアリング ジョブの実行の進行状況を監視し、スコアリング結果を確認する
> * バッチ エンドポイントに新しいデプロイを追加する
> * REST を使用してバッチ スコアリング ジョブを開始する

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション。Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure コマンド ライン インターフェイス (CLI) と ML 拡張機能。

Machine Learning 拡張機能には Azure CLI バージョン `>=2.15.0` が必要です。 この要件が満たされていることを確認します。

```azurecli
az version
```

必要に応じて、Azure CLI をアップグレードしてください。

```azurecli
az upgrade
```

> [!NOTE]
>
> `az upgrade` コマンドはバージョン 2.11.0 で追加されたもので、2.11.0 より前のバージョンでは動作しません。 以前のバージョンは、「[Azure CLI のインストール](/cli/azure/update-azure-cli)」の説明に従って再インストールすることによって更新できます。
>
> このコマンドを使用すると、インストールされているすべての拡張機能も既定で更新されます。 `az upgrade` オプションの詳細については、[コマンド リファレンス ページ](/cli/azure/reference-index#az_upgrade)を参照してください。

Azure ML 拡張機能を追加して構成します。

```azurecli
az extension add -n ml
```

ML 拡張機能の構成について詳しくは、[2.0 CLI (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。

* リポジトリの例

[AzureML Example リポジトリ](https://github.com/Azure/azureml-examples)をクローンします。 この記事では、`/cli/endpoints/batch` のアセットを使用しています。

## <a name="create-a-compute-target"></a>コンピューティング ターゲットを作成する

バッチ スコアリングは、クラウド コンピューティング リソースでのみ動作し、ローカルでは動作しません。 このクラウド コンピューティング リソースを "コンピューティング先" といいます。 コンピューティング先は、バッチ スコアリング ワークフローの実行場所となる再利用可能な仮想コンピューターです。

汎用的なコンピューティング先である [`AmlCompute`](/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py&preserve-view=true) を作成するには、次のコードを実行します。 コンピューティング先の詳細については、[Azure Machine Learning のコンピューティング先の概要](./concept-compute-target.md)に関するページを参照してください。

```azurecli
az ml compute create --name cpu-cluster --type AmlCompute --min-instances 0 --max-instances 5
```

## <a name="create-a-batch-endpoint"></a>バッチ エンドポイントを作成する

MLflow モデルを使用する場合は、バッチ エンドポイントのノー コード作成を利用できます。 つまり、スコアリング スクリプトと環境は、どちらも自動的に生成されるので、自分で用意する必要はありません。 詳細については、[MLflow と Azure Machine Learning を使用して ML モデルをトレーニングして追跡する方法 (プレビュー)](how-to-use-mlflow.md) に関するページを参照してください。

```azurecli
az ml endpoint create --type batch --file cli/endpoints/batch/create-batch-endpoint.yml
```

以下に示したのは、MLflow のバッチ エンドポイントを定義する YAML ファイルです。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/create-batch-endpoint.yml":::

| Key | 説明 |
| --- | ----------- |
| $schema | [省略可能] YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、バッチ エンドポイントの YAML ファイルで使用可能なすべてのオプションを確認できます。 |
| name | バッチ エンドポイントの名前。リージョン全体で一意である必要があります。 `name` の値は、スコアリング URI の一部として使用されます。 この値は、英語の文字で始まり、数字、文字、`-` 記号の組み合わせが続き、その後、数字または文字で終わる必要があります。 3 文字以上になるようにしてください。 検証には `^[a-zA-Z][-a-zA-Z0-9]+[a-zA-Z0-9]$` という正規表現が使用されます。|
| 型 | エンドポイントの種類。 バッチ エンドポイントには `batch` を使用します。 |
| auth_mode | Azure トークンベースの認証には `aad_token` を使用します。 |
| traffic | このデプロイにルーティングするトラフィックの割合 (%)。 バッチ エンドポイントの場合、`traffic` に有効な値は `0` と `100` のみです。 traffic の値が `100` のデプロイがアクティブとなります。 呼び出した際には、アクティブなデプロイにすべてのデータが送信されます。 |
| deployments | バッチ エンドポイントに作成されるデプロイの一覧。 この例では、`autolog-deployment` という名前のデプロイが 1 つあるだけです。 |

デプロイの属性:

| Key | 説明 |
| --- | ----------- |
| name | デプロイの名前。 |
| model | バッチ スコアリングに使用するモデル。 `name`、`version`、`local_path` を使用して、ローカル コンピューターからモデルをアップロードします。 ワークスペースで既存のモデル リソースを参照するには、`azureml:` プレフィックスを使用します。 たとえば、`azureml: autolog:1` は、`autolog` という名前のモデルのバージョン 1 を指します。 |
| compute.target | コンピューティング ターゲット。 ワークスペースで既存のコンピューティング リソースを参照するには、`azureml:` プレフィックスを使用します。 たとえば、`azureml:cpu-cluster` は、`cpu-cluster` という名前のコンピューティング先を指します。 |
| compute.instance_count | バッチ スコアリングに使用される計算ノードの数。 既定値は `1` です。|
| mini_batch_size | [省略可能] `scoring_script` が 1 回の `run()` 呼び出しで処理できるファイルの数。 既定値は `10` です。 |
| output_file_name | [省略可能] バッチ スコアリングの出力ファイルの名前。 既定値は `predictions.csv` です。 |
| retry_settings.max_retries | [省略可能] `scoring_script` `run()` が失敗した場合の最大再試行回数。 既定値は `3` です。 |
| retry_settings.timeout | [省略可能] `scoring_script` `run()` のタイムアウト (秒)。 既定値は `30` です。 |
| error_threshold | [省略可能] 無視するファイル エラーの数。 入力全体に対するエラーの数がこの値を超えると、ジョブは終了されます。 エラーのしきい値は入力全体を対象としています。`run()` メソッドに送信された個々のミニバッチを対象にしているものではありません。 既定値は `-1` です。この場合、実行が終了されることなく、エラーが何回でも許容されます。 | 
| logging_level | [省略可能] ログの詳細度。 値は詳細度が低い順に WARNING、INFO、DEBUG です。 既定値は INFO です。 |

## <a name="check-batch-endpoint-details"></a>バッチ エンドポイントの詳細を確認する

バッチ エンドポイントを作成した後は、`show` を使用して詳細を確認できます。 返されたデータから特定の属性のみを取得するには、[`--query parameter`](/cli/azure/query-azure-cli) を使用します。

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

## <a name="start-a-batch-scoring-job-using-cli"></a>CLI を使用してバッチ スコアリング ジョブを開始する

バッチ スコアリング ワークロードは、オフライン ジョブとして実行されます。 バッチ スコアリングは、大きなデータを処理するように設計されています。 入力は、コンピューティング クラスター上で並列処理されます。 データ パーティションは、ノード上のプロセスに割り当てられます。 複数のプロセスがある単一ノードでは、複数のパーティションが並列に実行されます。 既定では、バッチ スコアリングによって、スコアリング出力が Blob Storage に格納されます。 バッチ スコアリング ジョブは、CLI からデータ入力を渡すことで開始できます。 また、出力の場所を構成したり、最適なパフォーマンスを得るために一部の設定を上書きしたりすることもできます。

### <a name="start-a-bath-scoring-job-with-different-inputs-options"></a>異なる入力オプションでバッチ スコアリング ジョブを開始する

データの入力には、3 とおりの指定方法があります。

方法 1: 登録データ

`--input-data` を使用して、AML の登録データを渡します。

> [!NOTE]
> プレビュー期間中は、FileDataset のみサポートされます。 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-data azureml:<dataName>:<dataVersion>
```

方法 2: クラウド内のデータ

`--input-datastore` を使用して AML の登録データストアを指定し、`--input-path` を使用して、そのデータストアへの相対パスを指定します。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-datastore azureml:<datastoreName> --input-path <relativePath>
```

データがパブリックに公開されている場合は、`--input-path` を使用してそのパブリック パスを指定します。

記載した例を使用している場合、次のコマンドを実行することで、バッチ スコアリング ジョブを開始できます。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv
```

方法 3: ローカルの格納データ

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-local-path <localPath>
```

### <a name="configure-the-output-location"></a>出力の場所を構成する

既定では、バッチ スコアリングの結果が、ワークスペースの既定の BLOB ストアに格納されます。格納先は、ジョブ名 (システムにより生成された GUID) に基づいて命名されたフォルダーです。 スコアリング出力の格納場所は、バッチ スコアリング ジョブを開始するときに構成できます。 `--output-datastore` を使用して登録データストアを構成し、`--output-path` を使用して相対パスを構成します。 新しい出力ファイル名を構成するには、`--set output_file_name` を使用します。

> [!IMPORTANT]
> 一意の出力場所を使用する必要があります。 出力ファイルが存在する場合、バッチ スコアリング ジョブでエラーが発生します。 

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --output-datastore azureml:workspaceblobstore --output-path mypath --set output_file_name=mypredictions.csv
```

### <a name="overwrite-settings"></a>設定を上書きする

バッチ スコアリング ジョブを開始するとき、一部の設定を上書きすることで、コンピューティング リソースを有効活用し、パフォーマンスを高めることができます。 

* 異なるサイズの入力データが使用されている場合、`--mini-batch-size` を使用して `mini_batch_size` を上書きします。 
* このジョブに異なるコンピューティング リソースが必要な場合は、`--instance-count` を使用して `instance_count` を上書きします。 
* その他の設定、たとえば `max_retries`、`timeout`、`error_threshold` は、`--set` を使用して上書きします。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --set retry_settings.max_retries=1
```

## <a name="check-batch-scoring-job-execution-progress"></a>バッチ スコアリング ジョブ実行の進行状況を確認する

バッチ スコアリング ジョブは、入力のセット全体を処理するために、ある程度時間がかかるのが普通です。 ジョブの進行状況は、Azure Machine Learning スタジオから監視できます。 スタジオのリンクは、`invoke` の応答で、`interactionEndpoints.Studio.endpoint` の値として提供されます。

ジョブの詳細と状態は、CLI を使用して確認することもできます。

呼び出しの応答からジョブ名を取得します。

```azurecli
job_name=$(az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/nytaxi/taxi-tip-data.csv --query name -o tsv)
```

バッチ スコアリング ジョブの詳細と状態を確認するには、`job show` を使用します。

```azurecli
az ml job show --name $job_name
```

ジョブのログは、`job stream` を使用してストリーム配信します。

```azurecli
az ml job stream --name $job_name
```

## <a name="check-batch-scoring-results"></a>バッチ スコアリングの結果を確認する

スコアリングの結果を確認するには:

1. スタジオに移動します
1. **[実験]** に移動します
1. エンドポイントの実行 (`endpoint invoke` に対する応答の `interactionEndpoints.Studio.endpoint` の値) に移動します
1. 実行のグラフで、`batchscoring` ステップ内をクリックします
1. [出力 + ログ] タブを選択し、 **[Show data outputs]\(データ出力の表示\)** を選択します 
1. **[View output]\(出力の表示\)** アイコンを選択します :::image type="content" source="media/how-to-use-batch-endpoint/view-data-outputs.png" alt-text="データ出力の表示場所を示すスタジオのスクリーンショット" lightbox="media/how-to-use-batch-endpoint/view-data-outputs.png" :::
1. ポップアップ パネルでパスをコピーし、[Open Datastore]\(データストアを開く\) リンクを選択します。
1. 結果の BLOB ストア ページで、上記のパスを検索ボックスに貼り付けます。 このフォルダーには、スコアリングの出力が格納されます。
:::image type="content" source="media/how-to-use-batch-endpoint/scoring-view.gif" alt-text="score フォルダーとスコアリング出力を開く画面のスクリーンキャスト" lightbox="media/how-to-use-batch-endpoint/scoring-view.gif":::

## <a name="add-a-deployment-to-the-batch-endpoint"></a>デプロイをバッチ エンドポイントに追加する

1 つのバッチ エンドポイントには複数のデプロイを割り当てることができます。 それぞれのデプロイは、バッチ スコアリングに使用されるモデルを 1 つホストします。 

### <a name="add-a-new-deployment"></a>新しいデプロイを追加する

既存のバッチ エンドポイントに新しいデプロイを追加するには、次のコマンドを使用します。

```azurecli
az ml endpoint update --name mybatchedp --type batch --deployment-file cli/endpoints/batch/add-deployment.yml
```

このサンプルでは、MLflow 以外のモデルを使用します。 MLflow 以外のモデルを使用する場合は、YAML ファイルで環境とスコアリング スクリプトを指定する必要があります。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/batch/add-deployment.yml" :::

MLflow 以外のモデルには、他にも次のようなデプロイ属性があります。

| Key | 説明 |
| --- | ----------- |
| code_configuration.code.local_path | モデルをスコアリングするためのすべてのソース コードを格納するディレクトリ。 |
| code_configuration.scoring_script | 上記のディレクトリ内の Python ファイル。 このファイルには、`init()` 関数と `run()` 関数が記述されている必要があります。 `init()` 関数は、コストのかかる準備、または一般的な準備を行うときに使用します。 たとえば、これを使って、モデルをグローバル オブジェクトに読み込みます。 この関数は、プロセスの開始時に 1 回だけ呼び出されます。 各エントリをスコアリングするには、`run(mini_batch)` を使用します。`mini_batch` の値は、ファイル パスのリストです。 `run()` メソッドは、Pandas `DataFrame` または配列を返す必要があります。 その返された要素は、共通の出力ファイルに追加されます。 返された出力要素はそれぞれ、入力ミニバッチ内で成功した 1 つの入力要素の実行を示します。 特定の実行の出力結果に入力をマップできるだけの十分なデータが実行結果に含まれていることを確認してください。 実行の出力は出力ファイルに書き込まれますが、順序どおりの書き込みは保証されません。出力でいずれかのキーを使って、正しい入力にマップする必要があります。 |
| 環境 | コンピューティング先でモデルをスコアリングするための環境。 名前、バージョン、パスを指定することによってインラインで環境を定義できます。 `docker.image` 上にインストールされる依存関係を追加するには、`conda_file` を使用します。 既存の環境を参照するには、`azureml:` プレフィックスを使用します。 たとえば、`azureml: mnist-env:1` は、`mnist-env` という名前の環境のバージョン 1 を指します。 |

デプロイの詳細を確認するには、次を実行します。

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

### <a name="activate-the-new-deployment"></a>新しいデプロイをアクティブにする

バッチ推論では、問い合わせの 100% を目的のデプロイに送信する必要があります。 新しく作成したデプロイをターゲットとして設定するには、次を使用します。

```azurecli
az ml endpoint update --name mybatchedp --type batch --traffic mnist-deployment:100
```

デプロイの詳細を改めて観察すると、変更内容がわかります。

```azurecli
az ml endpoint show --name mybatchedp --type batch
```

これで、この新しいデプロイでバッチ スコアリング ジョブを呼び出すことができます。

```azurecli
az ml endpoint invoke --name mybatchedp --type batch --input-path https://pipelinedata.blob.core.windows.net/sampledata/mnist --mini-batch-size 10 --instance-count 2
```

## <a name="start-a-batch-scoring-job-using-rest"></a>REST を使用してバッチ スコアリング ジョブを開始する

バッチ エンドポイントには、REST アクセスに使用されるスコアリング URI が割り当てられます。 REST であれば、プラットフォームに関係なく、任意の HTTP ライブラリを使用してバッチ スコアリング ジョブを開始できます。

1. `scoring_uri` を取得します。  

```azurecli
scoring_uri=$(az ml endpoint show --name mybatchedp --type batch --query scoring_uri -o tsv)
```

2. アクセス トークンを取得します。

```azurecli
auth_token=$(az account get-access-token --query accessToken -o tsv)
```

3. `scoring_uri`、アクセス トークン、JSON データを使用して要求を POST し、バッチ スコアリング ジョブを開始します。

```bash
curl --location --request POST "$scoring_uri" --header "Authorization: Bearer $auth_token" --header 'Content-Type: application/json' --data-raw '{
"properties": {
  "dataset": {
    "dataInputType": "DataUrl",
    "Path": "https://pipelinedata.blob.core.windows.net/sampledata/mnist"
    }
  }
}'
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースを今後使用する予定がない場合は、課金が発生しないように削除します。

1. Azure portal で、左側のメニューにある **[リソース グループ]** を選択します。
1. リソース グループの一覧で、自分が作成したリソース グループを選択します。
1. **[リソース グループの削除]** を選択します。
1. リソース グループ名を入力します。 次に、 **[削除]** を選択します。

リソース グループは保持しつつ、いずれかのワークスペースを削除することもできます。 ワークスペースのプロパティを表示し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

この記事では、大量のデータをスコアリングできるよう、バッチ エンドポイントを作成して呼び出す方法について説明しました。 Azure Machine Learning の詳細については、以下の他の記事をご覧ください。

* [バッチ エンドポイントのトラブルシューティング](how-to-troubleshoot-batch-endpoints.md)
