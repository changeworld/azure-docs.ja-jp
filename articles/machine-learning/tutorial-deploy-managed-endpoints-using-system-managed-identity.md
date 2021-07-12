---
title: マネージド オンライン エンドポイントを使用して Azure リソースにアクセスする
titleSuffix: Azure Machine Learning
description: 機械学習モデルのデプロイを目的とした Azure リソースに対し、マネージド オンライン エンドポイントとシステム割り当てマネージド ID を使用して安全にアクセスします。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 05/25/2021
ms.topic: tutorial
ms.custom: tutorial
ms.openlocfilehash: 731d9a64c9ef144e8e51e9bce319a031056958ae
ms.sourcegitcommit: 8651d19fca8c5f709cbb22bfcbe2fd4a1c8e429f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/14/2021
ms.locfileid: "112071553"
---
# <a name="tutorial-access-azure-resources-with-a-managed-online-endpoint-and-system-managed-identity-preview"></a>チュートリアル: マネージド オンライン エンドポイントとシステム マネージド ID (プレビュー) を使用して Azure リソースにアクセスする

このチュートリアルでは、スコアリング スクリプトから、マネージド オンライン エンドポイントとシステム割り当てマネージド ID を使用して Azure リソースに安全にアクセスする方法について説明します。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]
このチュートリアルでは、Azure CLI とその ML 拡張機能を使用して次のアクションを実行する方法について説明します。

> [!div class="checklist"]
> * Azure CLI で使用する既定値を設定する
> * マネージド オンライン エンドポイントで使用する変数を構成する
> * BLOB ストレージ アカウントと BLOB コンテナーを作成する 
> * マネージド オンライン エンドポイントを作成する
> * システム割り当てマネージド ID にストレージへのアクセス許可を付与する
> * マネージド エンドポイントに関連付けられたデプロイを作成する


## <a name="prerequisites"></a>前提条件

* Azure Machine Learning を使用するためには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) を今すぐお試しください。

* Azure CLI と ML 拡張機能をインストールして構成する必要があります。 詳細については、[2.0 CLI (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。 

* Azure リソース グループがあること、また、そのリソース グループにおける `Contributor` アクセス権がご自身 (またはご使用のサービス プリンシパル) に割り当てられていることが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。 

* Azure Machine Learning ワークスペースが必要です。 上記の記事に従って ML 拡張機能を構成していれば、そのようなワークスペースが得られます。

* スコアリングとデプロイの準備が整ったトレーニング済みの機械学習モデル。


## <a name="set-the-defaults-for-azure-cli"></a>Azure CLI の既定値を設定する

このチュートリアルを通して適切なリソースを確実に用いるために、使用する Azure サブスクリプション ID、Azure Machine Learning ワークスペース、リソース グループの既定値を設定します。 そうすれば、Azure CLI コマンドを呼び出すたびに値を繰り返し渡す必要がなくなります。 

> [!IMPORTANT]
> ご使用のユーザー アカウントに、リソース グループへの "ユーザー アクセス管理者" ロールが割り当てられていることを確認してください。 

```azurecli
az account set --subscription <subscription id>
az configure --defaults workspace=<azureml workspace name> group=<resource group>
```

## <a name="define-the-configuration-yaml-file-for-your-deployment"></a>デプロイに使用する構成 YAML ファイルを定義する

マネージド エンドポイントをデプロイするにはまず、エンドポイントの構成を YAML ファイルで定義する必要があります。

以下のコード例では、次のようなマネージド エンドポイントを作成します。  
* `endpoints/online/managed/managed-identities/` ディレクトリの YAML ファイルを表示します。
* エンドポイントを参照する際に使用する名前 (`my-sai-endpoint`) を定義します。
* エンドポイントへのアクセスに使用する承認の種類 (`auth-mode: key`) を指定します。
* 作成するエンドポイントの種類が `online` であることを指定します。 
* エンドポイントに、`blue` というデプロイが関連付けられるように指定します
* デプロイするモデル、使用する環境とスコアリング スクリプトなど、デプロイの詳細を構成します。

:::code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::


## <a name="configure-variables-for-your-deployment"></a>デプロイ用の変数を構成する

作成するエンドポイント、ワークスペース、ワークスペースの場所について、それぞれの変数名を構成します。 次のコードは、これらの値をエンドポイントの環境変数としてエクスポートするものです。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

次に、BLOB ストレージ アカウント、BLOB コンテナー、ファイルに付けたい名前を指定します。 ここでこれらの変数名を定義しておいて、次のセクションの `az storage account create` コマンドや `az storage container create` コマンドから参照することになります。

次のコードは、それらの値を環境変数としてエクスポートするものです。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::


これらの変数をエクスポートした後、テキスト ファイルをローカルに作成します。 エンドポイントがデプロイされると、スコアリング スクリプトは、エンドポイントの作成時に生成されるシステム割り当てマネージド ID を使用して、このテキスト ファイルにアクセスします。

## <a name="create-blob-storage-and-container"></a>BLOB ストレージと BLOB コンテナーを作成する

この例では、BLOB ストレージ アカウントと BLOB コンテナーを作成し、先ほど作成したテキスト ファイルを BLOB コンテナーにアップロードします。 

まず、ストレージ アカウントを作成します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

次に、ストレージ アカウントに BLOB コンテナーを作成します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

その後、テキスト ファイルを BLOB コンテナーにアップロードします。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

## <a name="create-a-managed-online-endpoint"></a>マネージド オンライン エンドポイントを作成する

次のコードでは、デプロイを指定せずにマネージド オンライン エンドポイントを作成します。 デプロイの作成は、このチュートリアルで後ほど行います。

マネージド エンドポイントを作成すると、そのシステム割り当てマネージド ID が既定で作成されます。

>[!IMPORTANT]
> システム割り当てマネージド ID は不変であり、作成後は変更できません。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

エンドポイントの状態を次のようにして確認します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::


## <a name="give-storage-permission-to-system-assigned-managed-identity"></a>システム割り当てマネージド ID にストレージへのアクセス許可を与える

マネージド エンドポイントには、そのシステム割り当てマネージド ID を介してストレージへのアクセス許可を与えることができます。 

エンドポイントに対して作成されたシステム割り当てマネージド ID を取得します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

ここから、システム割り当てマネージド ID に、ご使用のストレージへのアクセス許可を与えることができます。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

## <a name="scoring-script-to-access-azure-resource"></a>Azure リソースにアクセスするためのスコアリング スクリプト

次のスコアリング スクリプトを参照すると、システム割り当てマネージド ID トークンを使用して Azure リソースにアクセスする方法がわかります。 このシナリオにおける Azure リソースとは、前のセクションで作成したストレージ アカウントです。 

:::code language="python" source="~/azureml-examples-main/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-using-your-configuration"></a>自分の構成を使用してデプロイを作成する

マネージド エンドポイントに関連付けられているデプロイを作成します。

基になる環境とイメージが初めて構築されるものであるかどうかに応じて、このデプロイには 8 分から 14 分程度かかる場合があります。 その後、同じ環境を使用したデプロイは、それよりも短時間で完了します。

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

デプロイの状態を確認します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

> [!NOTE]
> スコアリング スクリプトの init メソッドは、システム割り当てマネージド ID トークンを使用して、ストレージ アカウントからファイルを読み取ります。 

init メソッドの出力をチェックするには、次のコードでデプロイ ログを確認します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::

このコマンドが完了すると、モデル、環境、エンドポイントが自分の Azure Machine Learning ワークスペースに登録されます。

### <a name="confirm-your-endpoint-deployed-successfully"></a>エンドポイントが正しくデプロイされたことを確認する

エンドポイントがデプロイされた後、その操作を確認します。 推論の詳細は、モデルによって異なります。 このチュートリアルでは、JSON クエリ パラメーターは次のようになります。 

:::code language="json" source="~/azureml-examples-main/cli/endpoints/online/model-1/sample-request.json" :::

エンドポイントを呼び出すには、次を実行します。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::


## <a name="delete-the-endpoint-and-storage-account"></a>エンドポイントとストレージ アカウントを削除する

デプロイしたエンドポイントとストレージを今後使用する予定がない場合は、コストを節約するためにそれらを削除してください。 エンドポイントを削除すると、そこに関連付けられているデプロイもすべて削除されます。 

::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-main/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

## <a name="next-steps"></a>次のステップ

この Azure Machine Learning のチュートリアルでは、機械学習 CLI を使用して次の作業を行いました。

> [!div class="checklist"]
> * Azure CLI で使用する既定値を設定する
> * エンドポイントで使用する変数を構成する
> * BLOB ストレージ アカウントと BLOB コンテナーを作成する 
> * マネージド ディスクを作成する
> * システム割り当てマネージド ID にストレージへのアクセス許可を付与する
> * マネージド エンドポイントに関連付けられたデプロイを作成する

* CLI の使用について詳しくは、「[Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)」を参照してください。
* 特定のデータのみを返すように JSON クエリを調整する方法については、[Azure CLI コマンドの出力の照会](/cli/azure/query-azure-cli)に関するページを参照してください。
* YAML スキーマの詳細については、[オンライン エンドポイント YAML リファレンス](reference-online-endpoint-yaml.md)に関するドキュメントを参照してください。
