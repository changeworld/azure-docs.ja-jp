---
title: マネージド オンライン エンドポイントを使用して ML モデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure によって自動的に管理される Web サービスとして機械学習モデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/13/2021
ms.topic: how-to
ms.custom: how-to
ms.openlocfilehash: 8c14523d1d566086eff73693d6500947ccda7ba4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2021
ms.locfileid: "110382795"
---
# <a name="deploy-and-score-a-machine-learning-model-with-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)

マネージド オンライン エンドポイント (プレビュー) を使用すると、基盤となるインフラストラクチャを作成、管理することなくモデルをデプロイすることができます。 この記事では、まずローカル コンピューターにモデルをデプロイして、発生するエラーをデバッグした後、それを Azure にデプロイしてテストします。 ログを確認して、サービス レベル アグリーメント (SLA) を監視する方法についても説明します。 最初はモデルに取り組み、最終的には、オンライン (リアルタイム) スコアリングに使用できるスケーラブルな HTTPS/REST エンドポイントを完成させます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning を使用するためには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure CLI と ML 拡張機能をインストールして構成する必要があります。 詳細については、[2.0 CLI (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあること、また、そのリソース グループにおける `Contributor` アクセス権がご自身 (またはご使用のサービス プリンシパル) に割り当てられていることが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。 

* Azure Machine Learning ワークスペースが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなワークスペースが得られます。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 何度も値を渡さずに済むよう、次を実行します。

   ```azurecli
   az account set --subscription <subscription id>
   az configure --defaults workspace=<azureml workspace name> group=<resource group>

* [Optional] To deploy locally, you must have [Docker engine](https://docs.docker.com/engine/install/) running locally. This step is **highly recommended**. It will help you debug issues.

## Prepare your system

To follow along with the article, clone the samples repository, and navigate to the right directory by running the following commands:

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

エンドポイントの名前を設定します (以下の `YOUR_ENDPOINT_NAME` を一意の名前に変更します)。 以下のコマンドは、UNIX 環境用です。

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

Windows オペレーティング システムを使用する場合は、代わりに `set ENDPOINT_NAME=YOUR_ENDPOINT_NAME` コマンドを使用してください。

> [!NOTE]
> エンドポイントの名前は、Azure リージョン レベルで一意である必要があります。 たとえば、`westus2` に存在できる `my-endpoint` という名前のエンドポイントは 1 つだけです。 

## <a name="define-the-endpoint-configuration"></a>エンドポイントの構成を定義する

オンラインエンドポイントでモデルをデプロイするために必要な入力は次のとおりです。

- モデル ファイル (または、ワークスペースに既に登録されているモデルの名前とバージョン)。 この例では、回帰分析を行う `scikit-learn` モデルを使用します。
- モデルのスコアリングに必要なコード。 このケースでは、`score.py` ファイルがあります。
- モデルの実行環境 (環境とは Conda の依存関係を含んだ Docker イメージの場合もあれば、Dockerfile の場合もあります。この点については後述します)。
- インスタンスの種類とスケーリング キャパシティを指定するための設定。

次のスニペットは、上記の情報をすべてキャプチャする `endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml` ファイルを示しています。 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!Note]
> マネージド オンライン エンドポイント用に詳細に指定されたサンプル YAML があるので、[ご参考](https://azuremlschemas.azureedge.net/latest/managedOnlineEndpoint.template.yaml)ください

エンドポイントの YAML 形式のリファレンスを次に示します。 これらの属性を指定する方法については、この記事の YAML サンプルを参照するか、詳細に指定された前述の YAML サンプルを参照してください。 マネージド エンドポイントに関連した制限の詳細については、「[Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md)」を参照してください。

| Key | 説明 |
| --- | --- |
| $schema    | [省略可能] YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、YAML ファイルで使用可能なすべてのオプションを確認できます。|
| name       | エンドポイントの名前。 Azure リージョン レベルで一意である必要があります。|
| traffic | エンドポイントから各デプロイに転送するトラフィックの割合 (%)。 トラフィックの値は合計して 100 になる必要があります |
| auth_mode | キー ベース認証には `key` を、Azure Machine Learning トークンベース認証には `aml_token` を使用します。 `key` には有効期限がありませんが、`aml_token` には有効期限があります。 `az ml endpoint list-keys` コマンドを使用して最新のトークンを取得してください。 |
| deployments | エンドポイントに作成されるデプロイの一覧を含みます。 このケースでは、`blue` という名前のデプロイが 1 つあるだけです。 複数のデプロイの詳細については、「[オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)」を参照してください|

`deployment` の属性:

| Key | 説明 |
| --- | --- |
| name  | デプロイの名前 |
| model | この例では、モデルのプロパティ (`name`、`version`、`local_path`) をインラインで指定します。 モデル ファイルは自動的にアップロードされて登録されます。 インラインで指定することの欠点は、モデル ファイルを更新する場合に、バージョンを手動でインクリメントする必要がある点です。 関連するベスト プラクティスについては、以下のセクションの「**ヒント**」をお読みください。 |
| code_configuration.code.local_path | モデルのスコアリングに使用されるすべての Python ソース コードを格納するディレクトリ。 入れ子になったディレクトリまたはパッケージがサポートされます。 |
| code_configuration.scoring_script | 上記のスコアリング ディレクトリ内の Python ファイル。 この Python コードには、`init()` 関数と `run()` 関数が含まれている必要があります。 `init()` 関数は、モデルの作成後または更新後に呼び出されます (この関数を使用して、モデルをメモリにキャッシュするなどの操作を実行できます)。 `run()` 関数は、実際のスコアリングまたは予測を実行するためにエンドポイントが呼び出されるたびに呼び出されます。 |
| 環境 | モデルとコードのホストとなる環境の詳細を含みます。 この例では、`name`、`version`、`path` を含むインライン定義を使用しています。 この例では、イメージとして `environment.docker.image` が使用され、その上に `conda_file` の依存関係がインストールされます。 詳細については、以下のセクションの「**ヒント**」を参照してください。 |
| instance_type | デプロイ インスタンスのホストとなる VM の SKU。 詳細については、[マネージド オンライン エンドポイントでサポートされる VM SKU](reference-managed-online-endpoints-vm-sku-list.md) に関するページを参照してください。 |
| scale_settings.scale_type | 現在、この値は `manual` である必要があります。 エンドポイントとデプロイの作成後にスケールアップまたはスケールダウンするには、YAML で `instance_count` を更新し、`az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` コマンドを実行します。|
| scale_settings.instance_count | デプロイ内のインスタンスの数。 想定されるワークロードに基づく値を指定します。 高可用性を確保するために、Microsoft では `3` 以上に設定することを推奨しています。 |

> [!Note]
> マネージド エンドポイントの代わりに Azure Kubernetes Service (AKS) をコンピューティング先として使用するには:
> 1. [Azure ML スタジオを使用して](how-to-create-attach-compute-studio.md#whats-a-compute-target)、AKS クラスターを作成し、Azure Machine Learning ワークスペースにコンピューティング先としてアタッチします
> 2. AKS をコンピューティング先とするには、上記のマネージド エンドポイントの YAML の代わりに、この[エンドポイント YAML](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) を使用します。 `target` の値を、登録済みのコンピューティング先の名前に変更するには、YAML を編集する必要があります。
> この記事のコマンドは、オプションの SLA 監視と Log Analytics 統合を除き、マネージド エンドポイントと AKS エンドポイントのどちらにでも使用できます。

### <a name="registering-your-model-and-environment-separately"></a>モデルと環境を別々に登録する

 この例では、モデルと環境のプロパティ (`name`、`version`、およびアップロードするファイルの `local_path`) をインラインで指定します。 バックグラウンドで CLI がファイルをアップロードし、モデルと環境を自動的に登録します。 運用環境ではベスト プラクティスとして、モデルと環境を別々に登録し、登録済みの名前とバージョンを YAML で指定することをお勧めします。 その形式は `model:azureml:my-model:1` または `environment:azureml:my-env:1` です。

 登録するためには、`model` と `environment` の YAML 定義を別々の YAML ファイルに抽出し、`az ml model create` コマンドと `az ml environment create` コマンドを使用します。 これらのコマンドの詳細については、`az ml model create -h` と `az ml environment create -h` を実行してください。

### <a name="using-different-cpu--gpu-instance-types"></a>CPU と GPU のインスタンス タイプを変更する

上記の YAML では、汎用タイプ (`Standard_F2s_v2`) および非 GPU の Docker イメージが使用されています (YAML の `image` 属性を参照)。 GPU コンピューティングの場合は、GPU コンピューティング タイプの SKU と GPU の Docker イメージを選択する必要があります。

サポートされる汎用タイプと GPU インスタンス タイプは、[マネージド オンライン エンドポイントでサポートされる VM SKU](reference-managed-online-endpoints-vm-sku-list.md) に関するページでご覧いただけます。 Azure ML の CPU 基本イメージと GPU 基本イメージの一覧については、「[Azure Machine Learning の基本イメージ](https://github.com/Azure/AzureML-Containers)」を参照してください。

### <a name="using-more-than-one-model"></a>複数のモデルを使用する

現在、YAML で指定できるモデルは、デプロイごとに 1 つだけです。 複数のモデルがある場合、この制限を回避できます。モデルを登録するときに、すべてのモデルを (ファイルとして、またはサブディレクトリとして) 登録用のフォルダーにコピーします。 モデルのルート フォルダーのパスは、スコアリング スクリプトで環境変数 `AZUREML_MODEL_DIR` を使用して取得できます。基になるディレクトリ構造は維持されます。

## <a name="understand-the-scoring-script"></a>スコアリング スクリプトを理解する

> [!Tip]
> マネージド オンライン エンドポイントのスコアリング スクリプトの形式は、以前のバージョンの CLI や Python SDK で使用されている形式と同じです

上記の YAML にあるように、`code_configuration.scoring_script` には、`init()` 関数と `run()` 関数が含まれている必要があります。 この例では、この [score.py ファイル](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)を使用します。 `init()` 関数は、コンテナーの初期化 (起動) 時に呼び出されます。 この初期化は、通常、デプロイが作成または更新された直後に実行されます。 この例のようにモデルをメモリにキャッシュするなど、グローバルな初期化処理を実行するロジックが、ここには記述されます。 `run()` 関数は、エンドポイントが呼び出されるたびに呼び出されて、実際のスコアリングまたは予測を実行します。 この例では、JSON 入力からデータを抽出し、`scikit-learn` モデルの `predict()` メソッドを呼び出して結果を返します。

## <a name="deploy-and-debug-locally-using-local-endpoints"></a>ローカル エンドポイントを使用してデプロイとデバッグをローカルで行う

デバッグにかかる時間を短縮するために、エンドポイントはローカルでテスト実行することを **強くおすすめ** します。

> [!Note]
> * ローカルにデプロイするには、[Docker エンジン](https://docs.docker.com/engine/install/)をインストールしておく必要があります
> * Docker エンジンが実行されている必要があります。 通常は、開始時にエンジンも起動します。起動しない場合は、[こちらでトラブルシューティング](https://docs.docker.com/config/daemon/#start-the-daemon-manually)を行ってください

> [!Important]
> ローカル エンドポイント デプロイの目的は、Azure へのデプロイ前にコードと構成を検証してデバッグすることです。 ローカル デプロイには、次の制限があります。
> - ローカル エンドポイントでは、トラフィック ルール、認証、スケール設定、プローブ設定がサポート **されません**。 
> - ローカル エンドポイントでサポートされるデプロイは、エンドポイントごとに 1 つだけです。
> - 現在、ローカル デプロイでは、モデルと環境をインラインで指定する必要があります (サンプル YAML を参照)。 つまり、ローカル デプロイでは、Azure Machine Learning ワークスペースに登録されているモデルや環境の参照を使用することはできません。 

### <a name="deploy-the-model-locally"></a>モデルをローカルにデプロイする

モデルをローカルにデプロイするには、次のコマンドを実行します。

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

`--local` フラグは、エンドポイントを Docker 環境にデプロイするよう CLI に命令するものです。

>[!NOTE]
>Windows オペレーティング システムを使用している場合、こちらおよび以降のコマンドでは、`$ENDPOINT_NAME` ではなく `%ENDPOINT_NAME%` を使用してください

### <a name="check-if-the-local-deployment-succeeded"></a>ローカル デプロイが成功したかどうかを確認する

エラーが発生することなくモデルがデプロイされたかどうかを、ログを見て確認します。

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-with-your-model"></a>ローカル エンドポイントを呼び出し、モデルを使用してデータをスコアリングする

エンドポイントを呼び出してモデルをスコアリングするには、便利な `invoke` コマンドを使用して、JSON ファイルに格納されているクエリ パラメーターを渡します。

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

REST クライアント (curl など) を使用する場合は、スコアリング URI が必要となります。 これは、`az ml endpoint show --local -n $ENDPOINT_NAME` コマンドを使用して取得できます。 返されたデータには、`scoring_uri` という名前の属性があります。 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>呼び出し操作からの出力をログで確認する

サンプル `score.py` では、`run()` メソッドでいくつかの出力をコンソールにログしています。 この出力は、再度 `get-logs` コマンドを使用することで確認できます。

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>マネージド オンライン エンドポイントを Azure にデプロイする 

### <a name="deploy-to-azure"></a>Azure にデプロイ

YAML 構成をクラウドにデプロイするには、次のコマンドを実行します。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="deploy" :::

基になる環境とイメージが初めて構築されるものであるかどうかに応じて、このデプロイには 8 分から 14 分程度かかる場合があります。 その後、同じ環境を使用したデプロイは、それよりも短時間で完了します。

> [!Tip]
> CLI コンソールをブロックしたくない場合は、コマンドに `--no-wait` フラグを追加してください。 ただし、この場合、デプロイ状態が対話的に表示されなくなります。

> [!Tip]
> エラーのデバッグについては、[マネージド オンライン エンドポイントのデプロイのトラブルシューティング](how-to-troubleshoot-managed-online-endpoints.md)に関するページを参照してください。

### <a name="check-the-status-of-the-deployment"></a>デプロイの状態を確認する

`show` コマンドには、エンドポイントとデプロイの両方について `provisioning_status` があります。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="get_status" :::

`list` コマンドを使用すると、すべてのエンドポイントを表形式でワークスペースに一覧表示できます。

```azurecli
az ml endpoint list --output table
```

### <a name="check-if-the-cloud-deployment-succeeded"></a>クラウド デプロイが成功したかどうかを確認する

エラーが発生することなくモデルがデプロイされたかどうかを、ログを見て確認します。

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

既定では、ログが推論サーバーからプルされます。 (モデルやコードといったアセットをコンテナーにマウントする) storage-initializer からのログを表示したい場合は、`--container storage-initializer` フラグを追加します。

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>エンドポイントを呼び出し、モデルを使用してデータをスコアリングする

`invoke` コマンドまたは任意の REST クライアントを使用してエンドポイントを呼び出し、データをスコアリングすることができます。 

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

ここでも、先ほど紹介した `get-logs` コマンドを使用して、呼び出しログを確認できます。

REST クライアントを使用するには、`scoring_uri` のほか、認証キーと認証トークンが必要です。 `scoring_uri` は、`show` コマンドの出力から得られます。
 
::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

`--query` を使用して、必要な属性だけをフィルターで選択していることに注意してください。 `--query` の詳細については、[Azure CLI コマンドの出力のクエリ](/cli/azure/query-azure-cli)に関するページを参照してください。

`get-credentials` コマンドを使用して必要な資格情報を取得します。

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>[省略可能] デプロイを更新する

コード、モデル、環境、またはスケールの設定を更新する場合は、YAML ファイルを更新し、`az ml endpoint update` コマンドを実行します。 

>[!IMPORTANT]
> 1 回の `update` コマンドで変更できる要素 (トラフィック、スケール設定、コード、モデル、環境) は **1 つ** だけです。 

`update` の動作を理解するには:

1. ファイル `online/model-1/onlinescoring/score.py`を開きます。
1. `init()` 関数の最後の行を変更します。`logging.info("Init complete")` の後に、`logging.info("Updated successfully")` を追加してください。 
1. ファイルを保存します
1. 次のコマンドを実行します。
```azurecli
az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!IMPORTANT]
> YAML を使用した更新は宣言型です。 つまり YAML の変更は、基になる Azure Resource Manager リソース (エンドポイントとデプロイ) に反映されます。 このアプローチによって [GitOps](https://www.atlassian.com/git/tutorials/gitops) が促進されます。つまり、エンドポイントまたはデプロイに対する "*すべて*" の変更は YAML を経由することになります (`instance_count` も含む)。 副作用として、YAML からデプロイを削除し、そのファイルを使用して `az ml endpoint update` を実行した場合、そのデプロイは削除されます。 以下の「ヒント」で説明しているように、`--set ` フラグを使用すると、YAML を使用せずに更新を行うことができます。

5. エンドポイントの作成時または更新時に実行される `init()` 関数に変更を加えたため、"`Updated successfully` (正常に更新されました)" というメッセージがログに記録されます。 次を実行してログを取得します。
```azurecli
az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
```

まれなケースですが、解決できない問題のためにデプロイを削除して再作成する必要がある場合は、次のコマンドを使用します。

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

`update` コマンドは、ローカル エンドポイントでも使用できます。 同じ `az ml endpoint update` コマンドを `--local` フラグと共に使用します。

> [!Tip]
> `az ml endpoint update` コマンドでは、[Azure CLI の `--set` パラメーター](/cli/azure/use-cli-effectively#generic-update-arguments)を使用して、YAML の属性をオーバーライドするか、**または** YAML ファイルを渡さずに特定の属性を設定することができます。 個別の属性に対する `--set` の使用は、特に Dev/Test のシナリオで利便性を発揮します。 たとえば、最初のデプロイの `instance_count` をスケールアップするのであれば、`--set deployments[0].scale_settings.instance_count=2` フラグを使用できるでしょう。 ただし、YAML が更新されないため、この手法に [GitOps](https://www.atlassian.com/git/tutorials/gitops) を促進する効果はありません。

### <a name="optional-monitor-sla-using-azure-monitor"></a>[省略可能] Azure Monitor を使用して SLA を監視する

[マネージド オンライン エンドポイントの監視](how-to-monitor-online-endpoints.md)に関するページの手順に従って、メトリックを確認したり、SLA に基づくアラートを設定したりすることができます。

### <a name="optional-integrate-with-log-analytics"></a>[省略可能] Log Analytics と統合する

`get-logs` コマンドで取得できるのは、自動的に選択されたインスタンスの直近数百行のログだけです。 一方、Log Analytics は、ログを永続的に保存して分析する手段となります。 まず、「[Azure portal で Log Analytics ワークスペースを作成する](/azure/azure-monitor/logs/quick-create-workspace#create-a-workspace)」の手順に従って、Log Analytics ワークスペースを作成します。

次に、Azure portal で:

1. リソース グループに移動します
1. エンドポイントを選択します
1. **ARM リソース ページ** を選択します
1. **[診断設定]** を選択します
1. **[Add settings]\(設定の追加\)** を選択します。Log Analytics ワークスペースへのコンソール ログの送信を有効にします

ログが接続されるまでに最大 1 時間程度かかる場合があるので注意してください。 その後スコアリング要求を送信し、次の手順に従ってログを確認します。

1. Log Analytics ワークスペースを開く 
1. 左側のナビゲーション領域で **[ログ]** を選択します
1. 自動的に表示される **[クエリ]** ポップアップを閉じます
1. **[AmlOnlineEndpointConsoleLog]** をダブルクリックします
1. *[実行]* を選択します

## <a name="delete-the-endpoint-and-deployment"></a>エンドポイントとデプロイを削除する

今後使用する予定がない場合、以下のコマンドでデプロイを削除してください (エンドポイントとそこにあるすべてのデプロイが削除されます)。

::: code language="azurecli" source="~/azureml-examples-main/cli/how-to-deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>次の手順

- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)|
- [マネージド オンライン エンドポイントのデプロイのトラブルシューティング](how-to-troubleshoot-managed-online-endpoints.md)