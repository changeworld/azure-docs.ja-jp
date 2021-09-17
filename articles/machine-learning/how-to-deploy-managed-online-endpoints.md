---
title: マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイする
titleSuffix: Azure Machine Learning
description: Azure によって自動的に管理される Web サービスとして機械学習モデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 08/05/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 0c421148571b99bb182de6841586caafb116df42
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2021
ms.locfileid: "122418429"
---
# <a name="deploy-and-score-a-machine-learning-model-by-using-a-managed-online-endpoint-preview"></a>マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングする (プレビュー)

基になるインフラストラクチャを作成して管理する必要をなくすために、マネージド オンライン エンドポイント (プレビュー) を使用してモデルをデプロイする方法について説明します。 まずローカル コンピューターにモデルをデプロイして、発生するエラーをデバッグした後、それを Azure にデプロイしてテストします。 

ログを確認して、サービス レベル アグリーメント (SLA) を監視する方法についても説明します。 最初はモデルから開始し、最終的には、オンラインおよびリアルタイム スコアリングで使用できるスケーラブルな HTTPS/REST エンドポイントを完成させます。 

詳細については、「[Azure Machine Learning エンドポイント (プレビュー) とは](concept-endpoints.md)」を参照してください。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。

* Azure CLI と Azure CLI の `ml` 拡張機能をインストールして構成します。 詳細については、[CLI (v2) (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあり、それへの共同作成者アクセス権を自分 (または使用するサービス プリンシパル) が所持している必要があります。 リソース グループは、[CLI (v2) (プレビュー) のインストール、設定、使用](how-to-configure-cli.md)に関するページで作成されます。 

* Azure Machine Learning ワークスペースが必要です。 ワークスペースは、[CLI (v2) (プレビュー) のインストール、設定、使用](how-to-configure-cli.md)に関するページで作成されます。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 サブスクリプション、ワークスペース、およびリソース グループの値を複数回渡さないようにするには、次のコードを実行します。

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* (省略可能) ロ―カルでデプロイするには、ローカル コンピューターに [Docker エンジン](https://docs.docker.com/engine/install/)をインストールする必要があります。 問題のデバッグを容易にするため、このオプションを "*強くお勧めします*"。

## <a name="prepare-your-system"></a>システムを準備する

この記事に従って、まずサンプル リポジトリ (azureml-examples) を複製します。 次に、次のコードを実行してサンプル ディレクトリに移動します。

```azurecli
git clone https://github.com/Azure/azureml-examples
cd azureml-examples
cd cli
```

エンドポイント名を設定するには、オペレーティング システムに応じて、次のいずれかのコマンドを選択します (`YOUR_ENDPOINT_NAME` を一意の名前に置き換えてください)。

Unix の場合、次のコマンドを実行します。

```azurecli
export ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

Windows の場合、次のコマンドを実行します。

```azurecli
set ENDPOINT_NAME=YOUR_ENDPOINT_NAME
```

> [!NOTE]
> エンドポイント名は Azure リージョン内で一意である必要があります。 たとえば、Azure westus2 リージョンでは、`my-endpoint` という名前のエンドポイントは 1 つしか使用できません。 

## <a name="define-the-endpoint-configuration"></a>エンドポイントの構成を定義する

オンライン エンドポイントでモデルをデプロイするには、特定の入力が必要になります。

- モデル ファイル (または、ワークスペースに既に登録されているモデルの名前とバージョン)。 この例では、回帰を行う scikit-learn モデルを使用します。
- モデルのスコアに必要なコード。 このケースでは、*score.py* ファイルがあります。
- モデルが実行される環境。 ご覧のように、環境は Conda の依存関係を持つ Docker イメージか、Dockerfile である可能性があります。
- インスタンスの種類とスケーリング キャパシティを指定するための設定。

次のスニペットは *endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml* ファイルを示しています。これは、必要なすべての入力をキャプチャします。 

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml":::

> [!NOTE]
> YAML の完全な説明については、[マネージド オンライン エンドポイント (プレビュー) YAML リファレンス](reference-yaml-endpoint-managed-online.md)に関するページを参照してください。

エンドポイント YAML 形式のリファレンスを次の表で説明します。 これらの属性の指定方法については、「[システムを準備する](#prepare-your-system)」または[オンライン エンドポイント YAML リファレンス](reference-yaml-endpoint-managed-online.md)に関するページの YAML サンプルを参照してください。 マネージド エンドポイント関連の制限の詳細については、「[Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)」を参照してください。

| Key | 説明 |
| --- | --- |
| `$schema`    | (省略可能) YAML スキーマ。 上の例のスキーマをブラウザーで表示すると、YAML ファイルで使用可能なすべてのオプションを確認できます。|
| `name`       | エンドポイントの名前。 Azure リージョンで一意である必要があります。|
| `traffic` | エンドポイントから各デプロイに転送するトラフィックの割合 (%)。 トラフィック値の合計は 100 である必要があります。 |
| `auth_mode` | キーベース認証には `key` を、または、Azure Machine Learning トークンベース認証には `aml_token` を使用します。 `key` には有効期限がありませんが、`aml_token` には有効期限があります。 (`az ml endpoint get-credentials` コマンドを使用して最新のトークンを取得してください。) |
| `deployments` | エンドポイントに作成されるデプロイの一覧。 このケースでは、`blue` という名前のデプロイが 1 つあるだけです。 複数のデプロイの詳細については、「[オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)」を参照してください。|

次の表では、`deployments` の属性について説明します。

| Key | 説明 |
| --- | --- |
| `name`  | デプロイの名前。 |
| `model` | この例では、モデルのプロパティ (`name`、`version`、`local_path`) をインラインで指定します。 モデル ファイルは自動的にアップロードされ、登録されます。 インラインで指定することの欠点は、モデル ファイルを更新する場合に、バージョンを手動でインクリメントする必要がある点です。 関連するベスト プラクティスについては、次のセクションのヒントを参照してください。 |
| `code_configuration.code.local_path` | モデルのスコアリングに使用されるすべての Python ソース コードを格納するディレクトリ。 入れ子になったディレクトリとパッケージを使用できます。 |
| `code_configuration.scoring_script` | `code_configuration.code.local_path` スコアリング ディレクトリ内の Python ファイル。 この Python コードには、`init()` 関数と `run()` 関数が含まれている必要があります。 `init()` 関数は、モデルの作成後または更新後に呼び出されます (これを使用して、モデルをメモリにキャッシュするなどの操作を実行できます)。 `run()` 関数は、実際のスコアリングおよび予測を実行するために、エンドポイントが呼び出されるたびに呼び出されます。 |
| `environment` | モデルとコードのホストとなる環境の詳細を含みます。 この例では、`name`、`version`、`path` を含むインライン定義を使用しています。 イメージには `environment.docker.image` を使用します。 イメージ上に `conda_file` 依存関係がインストールされます。 詳細については、次のセクションのヒントを参照してください。 |
| `instance_type` | デプロイ インスタンスのホストとなる VM SKU。 詳細については、[マネージド オンライン エンドポイントでサポートされる VM SKU](reference-managed-online-endpoints-vm-sku-list.md) に関するページを参照してください。 |
| `scale_settings.scale_type` | 現在、この値は `manual` である必要があります。 エンドポイントとデプロイの作成後にスケールアップまたはスケールダウンするには、YAML で `instance_count` を更新し、`az ml endpoint update -n $ENDPOINT_NAME --file <yaml filepath>` コマンドを実行します。|
| `scale_settings.instance_count` | デプロイ内のインスタンスの数。 想定されるワークロードに基づく値を指定します。 高可用性を実現するには、`scale_settings.instance_count` を少なくとも `3` に設定することをお勧めします。 |

YAML スキーマの詳細については、[オンライン エンドポイント YAML リファレンス](reference-yaml-endpoint-managed-online.md)に関するドキュメントを参照してください。

> [!NOTE]
> コンピューティング先として、マネージド エンドポイントの代わりに Azure Kubernetes Service (AKS) を使用するには:
> 1. [Azure ML スタジオ](how-to-create-attach-compute-studio.md#whats-a-compute-target)を使用して、AKS クラスターを作成し、Azure Machine Learning ワークスペースにコンピューティング先としてアタッチします
> 1. AKS をターゲットにするには、マネージド エンドポイントの YAML の代わりに、[エンドポイント YAML](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/aks/simple-flow/1-create-aks-endpoint-with-blue.yml) を使用します。 `target` の値を、登録済みのコンピューティング先の名前に変更するには、YAML を編集する必要があります。
>
> この記事で使用されているコマンド (オプションの SLA 監視と Azure Log Analytics 統合を除く) はすべて、マネージド エンドポイントまたは AKS エンドポイントで使用できます。

### <a name="register-your-model-and-environment-separately"></a>モデルと環境を別々に登録する

この例では、モデルと環境のプロパティ (`name`、`version`、および`local_path` (ファイルのアップロード元)) をインラインで指定します。 CLI によってファイルが自動的にアップロードされ、モデルと環境が登録されます。 運用環境ではベスト プラクティスとして、モデルと環境を登録し、登録する名前とバージョンを YAML で個別に指定することをお勧めします。 `model: azureml:my-model:1` または `environment: azureml:my-env:1`.の形式を使用します。

登録するためには、`model` と `environment` の YAML 定義を別々の YAML ファイルに抽出し、`az ml model create` コマンドと `az ml environment create` コマンドを使用します。 これらのコマンドの詳細については、`az ml model create -h` と `az ml environment create -h` を実行してください。

### <a name="use-different-cpu-and-gpu-instance-types"></a>異なる CPU と GPU のインスタンス タイプを使用する

前述の YAML では、汎用タイプ (`Standard_F2s_v2`) および非 GPU の Docker イメージが使用されています (YAML の `image` 属性を参照)。 GPU コンピューティングの場合は、GPU コンピューティング タイプの SKU と GPU の Docker イメージを選択します。

サポートされる汎用タイプと GPU インスタンス タイプについては、[マネージド オンライン エンドポイントでサポートされる VM SKU](reference-managed-online-endpoints-vm-sku-list.md) に関するページを参照してください。 Azure Machine Learning CPU と GPU の基本イメージの一覧については、「[Azure Machine Learning 基本イメージ](https://github.com/Azure/AzureML-Containers)」を参照してください。

### <a name="use-more-than-one-model"></a>複数のモデルを使用する

現在、YAML で指定できるモデルは、デプロイごとに 1 つだけです。 複数のモデルがある場合には、モデルを登録するときに、すべてのモデルをファイルまたはサブディレクトリとして登録用のフォルダーにコピーします。 スコアリング スクリプトで、環境変数 `AZUREML_MODEL_DIR` を使用して、モデル ルート フォルダーへのパスを取得します。 基になるディレクトリ構造は保持されます。

## <a name="understand-the-scoring-script"></a>スコアリング スクリプトを理解する

> [!TIP]
> マネージド オンライン エンドポイントのスコアリング スクリプトの形式は、前のバージョンの CLI や Python SDK で使用されている形式と同じです。

前述のように、`code_configuration.scoring_script` には `init()` 関数と `run()` 関数が含まれている必要があります。 この例では、[score.py ファイル](https://github.com/Azure/azureml-examples/blob/main/cli/endpoints/online/model-1/onlinescoring/score.py)を使用します。 `init()` 関数は、コンテナーが初期化または起動された時に呼び出されます。 初期化は、通常、デプロイが作成または更新された直後に実行されます。 モデルをメモリにキャッシュするなど (この例のように)、グローバルな初期化処理を実行するロジックをここに記述します。 `run()` 関数は、エンドポイントが呼び出されるたびに呼び出され、実際のスコアリングと予測を実行します。 この例では、JSON 入力からデータを抽出し、scikit-learn モデルの `predict()` メソッドを呼び出し、結果を返します。

## <a name="deploy-and-debug-locally-by-using-local-endpoints"></a>ローカル エンドポイントを使用してデプロイとデバッグをローカルで行う

デバッグにかかる時間を短縮するために、エンドポイントはローカルでテスト実行することを "*強くお勧め*" します。

> [!NOTE]
> * ローカルにデプロイするには、[Docker エンジン](https://docs.docker.com/engine/install/)をインストールしておく必要があります。
> * Docker エンジンは実行中である必要があります。 通常、Docker エンジンは、コンピューターの起動時に起動します。 起動しない場合は、[Docker エンジンをトラブルシューティング](https://docs.docker.com/config/daemon/#start-the-daemon-manually)します。

> [!IMPORTANT]
> ローカル エンドポイント デプロイの目的は、Azure へのデプロイ前にコードと構成を検証してデバッグすることです。 ローカル デプロイには、次の制限があります。
> - ローカル エンドポイントでは、トラフィック ルール、認証、スケール設定、プローブ設定がサポート *されません*。 
> - ローカル エンドポイントでサポートされるデプロイは、エンドポイントごとに 1 つだけです。 つまり、ローカル デプロイでは、Azure Machine Learning ワークスペースに登録されているモデルや環境の参照を使用することはできません。 

### <a name="deploy-the-model-locally"></a>モデルをローカルにデプロイする

モデルをローカルにデプロイするには:

```azurecli
az ml endpoint create --local -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
```

> [!NOTE]
> Windows オペレーティング システムを使用している場合、こちらおよび以降のコマンドでは、`$ENDPOINT_NAME` ではなく `%ENDPOINT_NAME%` を使用してください

`--local` フラグは、エンドポイントを Docker 環境にデプロイするよう CLI に命令するものです。

### <a name="verify-the-local-deployment-succeeded"></a>ローカル デプロイが成功したかどうかを確認する

ログを確認し、エラーなしでモデルがデプロイされたかどうかを確認します。

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

### <a name="invoke-the-local-endpoint-to-score-data-by-using-your-model"></a>ローカル エンドポイントを呼び出し、モデルを使用してデータをスコアリングする

エンドポイントを呼び出してモデルをスコアリングするには、便利な `invoke` コマンドを使用して、JSON ファイルに格納されているクエリ パラメーターを渡します。

```azurecli
az ml endpoint invoke --local -n $ENDPOINT_NAME --request-file endpoints/online/model-1/sample-request.json
```

REST クライアント (curl など) を使用する場合は、スコアリング URI が必要です。 スコアリング URI を取得するには、`az ml endpoint show --local -n $ENDPOINT_NAME` を実行します。 返された値で、`scoring_uri` 属性を探します。 

### <a name="review-the-logs-for-output-from-the-invoke-operation"></a>呼び出し操作からの出力をログで確認する

例の *score.py* ファイルでは、`run()` メソッドがいくつかの出力をコンソールにログしています。 この出力は、再度 `get-logs` コマンドを使用することで確認できます。

```azurecli
az ml endpoint get-logs --local -n $ENDPOINT_NAME --deployment blue
```

##  <a name="deploy-your-managed-online-endpoint-to-azure"></a>マネージド オンライン エンドポイントを Azure にデプロイする

次に、マネージド オンライン エンドポイントを Azure にデプロイします。

### <a name="deploy-to-azure"></a>Azure にデプロイ

YAML 構成をクラウドにデプロイするには、次のコードを実行します。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="deploy" :::

基になる環境またはイメージが初めて構築されるものであるかどうかに応じて、このデプロイには最大で 15 分かかる場合があります。 同じ環境を使用する後続のデプロイでは、処理がより迅速に完了します。

> [!TIP]
> * CLI コンソールをブロックしたくない場合は、コマンドに `--no-wait` フラグを追加してください。 ただし、この場合、デプロイ状態が対話的に表示されなくなります。
>
> * エラーのデバッグについては、[マネージド オンライン エンドポイントのデプロイのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md) に関するページを参照してください。

### <a name="check-the-status-of-the-deployment"></a>デプロイの状態を確認する

`show` コマンドには、`provisioning_status` にエンドポイントとデプロイの情報が含まれます。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_status" :::

`list` コマンドを使用すると、ワークスペースのすべてのエンドポイントを表形式で一覧表示できます。

```azurecli
az ml endpoint list --output table
```

### <a name="check-the-status-of-the-cloud-deployment"></a>クラウド デプロイの状態を確認する

ログを確認し、エラーなしでモデルがデプロイされたかどうかを確認します。

:::code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_logs" :::

既定では、ログが推論サーバーからプルされます。 ストレージ初期化子 (モデルやコードなどのアセットをコンテナーにマウントする) からのログを表示するには、`--container storage-initializer` フラグを追加します。

### <a name="invoke-the-endpoint-to-score-data-by-using-your-model"></a>エンドポイントを呼び出し、モデルを使用してデータをスコアリングする

`invoke` コマンドまたは任意の REST クライアントを使用してエンドポイントを呼び出し、データをスコアリングすることができます。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="test_endpoint" :::

起動ログを表示するには、再度 `get-logs` を実行します。

REST クライアントを使用するには、`scoring_uri` の値と認証キーまたはトークンが必要です。 `scoring_uri` 値は、`show` コマンドの出力から得られます。
 
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="get_scoring_uri" :::

`--query` フラグを使用して、必要な属性だけをフィルターしています。 `--query` の詳細については、[Azure CLI コマンドの出力のクエリ](/cli/azure/query-azure-cli)に関するページを参照してください。

`get-credentials` コマンドを使用して必要な資格情報を取得します。

```azurecli
az ml endpoint get-credentials -n $ENDPOINT_NAME
```

### <a name="optional-update-the-deployment"></a>(省略可能) デプロイを更新する

コード、モデル、環境、またはスケールの設定を更新する場合は、YAML ファイルを更新し、次に `az ml endpoint update` コマンドを実行します。 

> [!IMPORTANT]
> 1 回の `update` コマンドで変更できる要素 (トラフィック、スケール設定、コード、モデル、環境) は "*1 つ*" だけです。 

`update` の動作を理解するには:

1. ファイル *online/model-1/onlinescoring/score.py* を開きます。
1. `init()` 関数の最後の行を変更します。`logging.info("Init complete")` の後に、`logging.info("Updated successfully")` を追加してください。 
1. ファイルを保存します。
1. 次のコマンドを実行します。

    ```azurecli
    az ml endpoint update -n $ENDPOINT_NAME -f endpoints/online/managed/simple-flow/1-create-endpoint-with-blue.yml
    ```

    > [!IMPORTANT]
    > YAML を使用した更新は宣言型です。 つまり YAML の変更は、基になる Azure Resource Manager リソース (エンドポイントとデプロイ) に反映されます。 この宣言型アプローチによって [GitOps](https://www.atlassian.com/git/tutorials/gitops) が促進されます。つまり、エンドポイントとデプロイに対する "*すべて*" の変更は YAML を経由することになります (`instance_count` も含む)。 その結果、YAML からデプロイを削除し、そのファイルを使用して `az ml endpoint update` を実行した場合、そのデプロイは削除されます。 `--set` フラグを使用して、YAML を使用せずに更新を行うことができます。
    
1. `init()` 関数 (`init()` はエンドポイントが作成または更新されると実行される) に変更を加えたため、`Updated successfully` というメッセージがログに記録されます。 次を実行してログを取得します。

    ```azurecli
    az ml endpoint get-logs -n $ENDPOINT_NAME --deployment blue
    ```

まれなケースですが、解決できない問題のためにデプロイを削除して再作成する必要がある場合は、次を実行します。

```azurecli
az ml endpoint delete -n $ENDPOINT_NAME --deployment blue
```

`update` コマンドは、ローカル エンドポイントでも使用できます。 同じ `az ml endpoint update` コマンドを `--local` フラグと共に使用します。

> [!TIP]
> `az ml endpoint update` コマンドでは、[Azure CLI の `--set` パラメーター](/cli/azure/use-cli-effectively#generic-update-arguments)を使用して、YAML の属性をオーバーライドするか、"*または*" YAML ファイルを渡さずに特定の属性を設定することができます。 個別の属性に対する `--set` の使用は、特に開発およびテストのシナリオで利便性を発揮します。 たとえば、最初のデプロイの `instance_count` 値をスケールアップするのであれば、`--set deployments[0].scale_settings.instance_count=2` フラグを使用できます。 ただし、YAML が更新されないため、この手法に [GitOps](https://www.atlassian.com/git/tutorials/gitops) を促進する効果はありません。

### <a name="optional-monitor-sla-by-using-azure-monitor"></a>(省略可能) Azure Monitor を使用して SLA を監視する

メトリックを表示し、SLA に基づいてアラートを設定するには、[マネージド オンライン エンドポイントの監視](how-to-monitor-online-endpoints.md)に関するセクションの手順を実行します。

### <a name="optional-integrate-with-log-analytics"></a>(省略可能) Log Analytics と統合する

`get-logs` コマンドで取得できるのは、自動的に選択されたインスタンスの最後の数百行のログだけです。 一方、Log Analytics は、ログを永続的に保存して分析する手段となります。 

まず、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md#create-a-workspace)」の手順に従って、Log Analytics ワークスペースを作成します。

次に、Azure portal で:

1. リソース グループに移動します。
1. エンドポイントを選択します。
1. **ARM リソース ページ** を選択します。
1. **[診断設定]** を選択します。
1. **[設定の追加]** を選択します。
1. Log Analytics ワークスペースへのコンソール ログの送信を有効にします。

ログの接続には最大 1 時間かかる場合があります。 1 時間後にスコアリング要求を送信し、次の手順に従ってログを確認します。

1. Log Analytics ワークスペースを開きます。 
1. 左のメニューで **[ログ]** を選択します。
1. 自動的に表示される **[クエリ]** ダイアログを閉じます。
1. **[AmlOnlineEndpointConsoleLog]** をダブルクリックします。
1. **[実行]** を選択します。

## <a name="delete-the-endpoint-and-the-deployment"></a>エンドポイントとデプロイを削除する

デプロイを今後使用する予定がない場合、次のコードを実行してデプロイを削除してください (エンドポイントと基になるすべてのデプロイが削除されます)。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint.sh" ID="delete_endpoint" :::

## <a name="next-steps"></a>次のステップ

詳細については、次の記事を参照してください。

- [REST を使用してモデルをデプロイする (プレビュー)](how-to-deploy-with-rest.md)
- [スタジオでマネージド オンライン エンドポイント (プレビュー) を作成および使用する](how-to-use-managed-online-endpoint-studio.md)
- [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md)
- [バッチ エンドポイント (プレビュー) を使用したバッチ スコアリング](how-to-use-batch-endpoint.md)
- [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)
- [チュートリアル: マネージド オンライン エンドポイントとシステム マネージド ID を使用して Azure リソースにアクセスする (プレビュー)](tutorial-deploy-managed-endpoints-using-system-managed-identity.md)
- [マネージド オンライン エンドポイントのデプロイトをラブルシューティングする](how-to-troubleshoot-managed-online-endpoints.md)
