---
title: CLI 拡張機能
titleSuffix: Azure Machine Learning
description: Azure CLI 用の Azure Machine Learning CLI 拡張機能について説明します。 Azure CLI は、Azure クラウド上のリソースを操作できるようにするための、クロスプラットフォームのコマンド ライン ユーティリティです。 Machine Learning 拡張機能を使用すると、Azure Machine Learning を操作することができます。 ML CLI を使うと、ワークスペース、データストア、データセット、パイプライン、モデル、デプロイなどのリソースを作成および管理できます。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: 471b26ebc4bd4aecb814ec43c7eba56e3d764fa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402495"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Azure Machine Learning 用の CLI 拡張機能を使用する
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning CLI は [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) への拡張機能であり、Azure プラットフォーム向けのクロスプラット フォームのコマンド ライン インターフェイスです。 この拡張機能では、Azure Machine Learning を操作するためのコマンドが提供されます。 それにより、機械学習のアクティビティを自動化することができます。 CLI 拡張機能を使用することで可能になるアクションの例を次に示します。

+ 実験を実行して機械学習モデルを作成します

+ 顧客が使用できるように機械学習モデルを登録します

+ 機械学習モデルのライフサイクルをパッケージ化、デプロイ、追跡します

CLI は、Azure Machine Learning SDK に取って代わるものではありません。 自動化に適合するために高度にパラメーター化されたタスクを処理するように最適化された補完的ツールです。

## <a name="prerequisites"></a>前提条件

* CLI を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)。

## <a name="full-reference-docs"></a>詳細なリファレンス ドキュメント

[Azure CLI の azure-cli-ml 拡張機能に関する詳細なリファレンス ドキュメント](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest)を参照してください。

## <a name="install-the-extension"></a>拡張機能をインストールする

Machine Learning CLI 拡張機能をインストールするには、次のコマンドを使用します。

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> 以下のコマンドで使用できるサンプル ファイルは、[こちら](https://aka.ms/azml-deploy-cloud)にあります。

メッセージが表示されたら、`y` を選択して拡張機能をインストールします。

拡張機能がインストールされたことを確認するには、次のコマンドを使用して、ML 固有のサブコマンドの一覧を表示します。

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>拡張機能を更新する

Machine Learning CLI 拡張機能を更新するには、次のコマンドを使用します。

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>拡張機能を削除する

CLI 拡張機能を削除するには、次のコマンドを使用します。

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>リソース管理

次のコマンドは、CLI を使用して、Azure Machine Learning によって使用されるリソースを管理する方法を示したものです。

+ リソース グループがまだない場合は、次のように指定してリソース グループを作成します。

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Azure Machine Learning ワークスペースを作成します。

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    > [!TIP]
    > このコマンドでは、Basic エディションのワークスペースが作成されます。 Enterprise ワークスペースを作成するには、`--sku enterprise` コマンドで `az ml workspace create` スイッチを使用します。 Azure Machine Learning のエディションについて詳しくは、「[Azure Machine Learning とは](overview-what-is-azure-ml.md#sku)」を参照してください。

    詳しくは、「[az ml workspace create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create)」をご覧ください。

+ ワークスペース構成をフォルダーに接続して、CLI コンテキストの認識を有効にします。

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    このコマンドでは、サンプルの runconfig および conda 環境ファイルを含む `.azureml` サブディレクトリを作成します。 これには、Azure Machine Learning ワークスペースとの通信に使用される `config.json` ファイルも含まれています。

    詳しくは、「[az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach)」をご覧ください。

+ Azure BLOB コンテナーをデータストアとして接続します。

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    詳しくは、「[az ml datastore attach-blob](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob)」をご覧ください。

+ データストアにファイルをアップロードします。

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    詳細については、[az ml datastore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload)に関するページを参照してください。

+ AKS クラスターをコンピューティング先として接続します。

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    詳しくは、「[az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)」をご覧ください。

+ 新しい AMLcompute ターゲットを作成します。

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    詳しくは、「[az ml computetarget create amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute)」をご覧ください。

## <a name="run-experiments"></a><a id="experiments"></a>実験の実行

* 実験の実行を開始する。 このコマンドを使用する場合は、-c パラメーターに対して runconfig ファイルの名前 (ファイル システムが表示されている場合、\*.runconfig の前のテキスト) を指定します。

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` コマンドでは、2 つのサンプル runconfig ファイルを含む `.azureml` サブディレクトリが作成されます。 
    >
    > プログラムで実行構成オブジェクトを作成する Python スクリプトがある場合は、[RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) を使用してそれを runconfig ファイルとして保存できます。
    >
    > 完全な runconfig のスキーマについては、こちらの [JSON ファイル](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)を参照してください。 スキーマは、各オブジェクトの `description` キーを使用して自己文書化されています。 また、有効な値の列挙と、末尾にテンプレートのスニペットがあります。

    詳しくは、「[az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script)」をご覧ください。

* 次のように指定して、実験の一覧を表示します。

    ```azurecli-interactive
    az ml experiment list
    ```

    詳しくは、「[az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list)」をご覧ください。

## <a name="dataset-management"></a>データセットの管理

次のコマンドは、Azure Machine Learning でデータセットを操作する方法を示しています。

+ データセットを登録する:

    ```azurecli-interactive
    az ml dataset register -f mydataset.json
    ```

    データセットを定義する場合に使用する JSON ファイルの形式の詳細については、`az ml dataset register --show-template` を使用してください。

    詳細については、「[az ml dataset register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ アクティブまたは非推奨のデータセットをアーカイブする:

    ```azurecli-interactive
    az ml dataset archive -n dataset-name
    ```

    詳細については、「[az ml dataset archive](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ データセットを非推奨にする:

    ```azurecli-interactive
    az ml dataset deprecate -d replacement-dataset-id -n dataset-to-deprecate
    ```

    詳細については、「[az ml dataset deprecate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ ワークスペース内のすべてのデータセットを一覧表示する:

    ```azurecli-interactive
    az ml dataset list
    ```

    詳細については、「[az ml dataset list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ データセットの詳細を取得する:

    ```azurecli-interactive
    az ml dataset show -n dataset-name
    ```

    詳細については、「[az ml dataset show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ アーカイブ済みまたは非推奨のデータセットを再アクティブ化する:

    ```azurecli-interactive
    az ml dataset reactivate -n dataset-name
    ```

    詳細については、「[az ml dataset reactivate](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

+ データセットの登録を解除する:

    ```azurecli-interactive
    az ml dataset unregister -n dataset-name
    ```

    詳細については、「[az ml dataset unregister](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/dataset?view=azure-cli-latest#ext-azure-cli-ml-az-ml-dataset-archive)」を参照してください。

## <a name="environment-management"></a>環境の管理

次のコマンドで、お使いのワークスペース用の Azure Machine Learning [環境](how-to-configure-environment.md)を作成、登録、および一覧表示する方法を示します。

+ 環境のスキャフォールディング ファイルを作成します。

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    詳細については、[az ml environment scaffold](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)に関するページを参照してください。

+ 環境を登録します。

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    詳細については、[az ml environment register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register)に関するページを参照してください。

+ 登録されている環境を一覧表示します。

    ```azurecli-interactive
    az ml environment list
    ```

    詳細については、[az ml environment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list)に関するページを参照してください。

+ 登録されている環境をダウンロードします。

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    詳細については、[az ml environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download)に関するページを参照してください。

### <a name="environment-configuration-schema"></a>環境構成スキーマ

`az ml environment scaffold` コマンドを使用した場合、CLI でカスタム環境構成を作成するために変更および使用できるテンプレートの `azureml_environment.json` ファイルが生成されます。 最上位レベルのオブジェクトは、Python SDK の [`Environment`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) クラスに緩やかにマップされます。 

```json
{
    "name": "testenv",
    "version": null,
    "environmentVariables": {
        "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
    },
    "python": {
        "userManagedDependencies": false,
        "interpreterPath": "python",
        "condaDependenciesFile": null,
        "baseCondaEnvironment": null
    },
    "docker": {
        "enabled": false,
        "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
        "baseDockerfile": null,
        "sharedVolumes": true,
        "shmSize": "2g",
        "arguments": [],
        "baseImageRegistry": {
            "address": null,
            "username": null,
            "password": null
        }
    },
    "spark": {
        "repositories": [],
        "packages": [],
        "precachePackages": true
    },
    "databricks": {
        "mavenLibraries": [],
        "pypiLibraries": [],
        "rcranLibraries": [],
        "jarLibraries": [],
        "eggLibraries": []
    },
    "inferencingStackVersion": null
}
```

次の表は、JSON ファイルの各最上位レベルのフィールド、その種類、説明の詳細を示しています。 オブジェクトの種類が Python SDK のクラスにリンクされている場合、各 JSON フィールドと、Python クラスのパブリック変数名の間には緩い 1 対 1 の一致が存在します。 場合によっては、フィールドがクラス変数ではなく、コンストラクター引数にマップされることがあります。 たとえば、`environmentVariables` フィールドは `environment_variables`[`Environment` クラスの ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment(class)?view=azure-ml-py) 変数にマップされます。

| JSON フィールド | 種類 | 説明 |
|---|---|---|
| `name` | `string` | 環境の名前。 名前を **Microsoft** や **AzureML** で開始しないでください。 |
| `version` | `string` | 環境のバージョン。 |
| `environmentVariables` | `{string: string}` | 環境変数の名前と値のハッシュ マップ。 |
| `python` | [`PythonSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.pythonsection?view=azure-ml-py) | ターゲット コンピューティング リソースで使用する Python 環境とインタープリターを定義するオブジェクト。 |
| `docker` | [`DockerSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.dockersection?view=azure-ml-py) | 環境の仕様に合わせて構築された Docker イメージをカスタマイズするための設定を定義します。 |
| `spark` | [`SparkSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.sparksection?view=azure-ml-py) | セクションで Spark 設定が構成されます。 これは、フレームワークが PySpark に設定されている場合にのみ使用されます。 |
| `databricks` | [`DatabricksSection`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.databricks.databrickssection?view=azure-ml-py) | Databricks ライブラリの依存関係を構成します。 |
| `inferencingStackVersion` | `string` | イメージに追加される推論スタックのバージョンを指定します。 推論スタックを追加しないようにするには、このフィールドを `null` のままにします。 有効な値: "latest"。 |

## <a name="ml-pipeline-management"></a>ML パイプラインの管理

次のコマンドは、機械学習パイプラインを操作する方法を示しています。

+ 機械学習パイプラインを作成する:

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    詳しくは、「[az ml pipeline create](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create)」をご覧ください。

    パイプラインの YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md)」をご覧ください。

+ パイプラインの実行:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    詳しくは、「[az ml run submit-pipeline](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline)」をご覧ください。

    パイプラインの YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md)」をご覧ください。

+ パイプラインのスケジュールを設定する:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    詳しくは、「[az ml pipeline create-schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule)」をご覧ください。

    パイプラインのスケジュールに関する YAML ファイルについて詳しくは、「[YAML で機械学習パイプラインを定義する](reference-pipeline-yaml.md#schedules)」をご覧ください。

## <a name="model-registration-profiling-deployment"></a>モデルの登録、プロファイル、デプロイ

次のコマンドは、トレーニング済みモデルを登録し、それを運用サービスとしてデプロイする方法を示したものです。

+ Azure Machine Learning にモデルを登録する:

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    詳しくは、「[az ml model register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register)」をご覧ください。

+ **省略可能** モデルをプロファイルして、デプロイに最適な CPU とメモリの値を取得します。
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    詳しくは、「[az ml model profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile)」をご覧ください。

+ モデルを AKS にデプロイします｡
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    推論構成ファイル スキーマの詳細については、「[推論構成スキーマ](#inferenceconfig)」を参照してください。
    
    デプロイ構成ファイル スキーマの詳細については、「[デプロイ構成スキーマ](#deploymentconfig)」を参照してください。

    詳しくは、「[az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy)」をご覧ください。

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>推論構成スキーマ

[!INCLUDE [inferenceconfig](../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>デプロイ構成スキーマ

### <a name="local-deployment-configuration-schema"></a>ローカル デプロイ構成スキーマ

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Azure コンテナー インスタンス デプロイ構成スキーマ 

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Azure Kubernetes Service デプロイ構成スキーマ

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>次のステップ

* [Machine Learning CLI 拡張機能のコマンド リファレンス](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest)

* [Azure Pipelines を使用して機械学習モデルをトレーニングおよびデプロイする](/azure/devops/pipelines/targets/azure-machine-learning)
