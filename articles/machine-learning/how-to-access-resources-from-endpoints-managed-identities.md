---
title: マネージド エンドポイントから Azure リソースにアクセスする
titleSuffix: Azure Machine Learning
description: 自分の機械学習モデルをデプロイするための Azure リソースに、システム割り当てマネージド ID またはユーザー割り当てマネージド ID を使用して、マネージド オンライン エンドポイントから安全にアクセスする方法を説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: seramasu
ms.reviewer: laobri
author: rsethur
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: ff8778dfa69552deaf26dd13438ac73fed260658
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565659"
---
# <a name="access-azure-resources-from-a-managed-online-endpoint-preview-with-a-managed-identity"></a>マネージド ID を使用してマネージド オンライン ・んどポイント (プレビュー) から Azure リソースにアクセスする 

マネージド オンライン エンドポイントと、システム割り当てマネージド ID またはユーザー割り当てマネージド ID のいずれかを使用して、スコアリング スクリプトから Azure リソースにアクセスする方法を説明します。 

マネージド エンドポイント (プレビュー) を使用すると、Azure Machine Learning で、コンピューティング リソースのプロビジョニングと機械学習モデルのデプロイの負担を管理できます。 通常、モデルは、Azure Container Registry や推論用の blob storage などの Azure リソースにアクセスする必要がありますが、マネージド ID を使用すると、コード内で資格情報を管理することなく、それらのリソースにアクセスできます。 [マネージド ID の詳細を確認してください](../active-directory/managed-identities-azure-resources/overview.md)。

このガイドでは、マネージド ID、ストレージ アカウント、またはマネージド オンライン エンドポイントがないことを前提としています。 これらのコンポーネントをすでに持っている場合は、[マネージド id にアクセス許可を付与する](#give-access-permission-to-the-managed-identity)セクションに進んでください。 

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* Azure Machine Learning を使用するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に無料アカウントを作成してください。 [無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) を今すぐお試しください。

* Azure CLI と ML 拡張機能をインストールして構成します。 詳細については、[2.0 CLI (プレビュー) のインストール、セットアップ、使用](how-to-configure-cli.md)に関するページを参照してください。

* Azure Resource グループが必要です。そのグループで (または使用するサービス プリンシパルで) `User Access Administrator` と `Contributor` のアクセス権を持っている必要があります。 上記の記事に従って ML 拡張機能を構成していれば、そのようなリソース グループが得られます。

* Azure Machine Learning ワークスペース。 上記の記事に従って ML 拡張機能を構成すれば、ワークスペースを用意できます。

* スコアリングとデプロイの準備が整ったトレーニング済みの機械学習モデル。 サンプルに従っている場合は、モデルは用意されています。

* まだ Azure CLI の既定値を設定していない場合は、既定の設定を保存する必要があります。 サブスクリプション、ワークスペース、およびリソース グループの値を複数回渡さないようにするには、次のコードを実行します。

   ```azurecli
   az account set --subscription <subscription ID>
   az configure --defaults workspace=<Azure Machine Learning workspace name> group=<resource group>
   ```

* サンプルに従って処理するには、サンプル リポジトリを複製します。

    ```azurecli
    git clone https://github.com/Azure/azureml-examples --depth 1
    cd azureml-examples/cli
    ```

## <a name="define-configuration-yaml-file-for-deployment"></a>デプロイに使用する構成 YAML ファイルを定義する

CLI を使用してマネージド エンドポイントをデプロイするには、YAML ファイルで構成を定義する必要があります。 YAML スキーマの詳細については、[オンライン エンドポイント YAML リファレンス](reference-yaml-endpoint-managed-online.md)に関するドキュメントを参照してください。

次の例の YAML ファイルは、オンライン エンドポイントを作成するために使用されます。 

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

次の YAML の例は `endpoints/online/managed/managed-identities/1-sai-create-endpoint` にあります。 このファイルによって、以下が行われます。 

* エンドポイントを参照する際に使用する名前 (`my-sai-endpoint`) を定義します。
* エンドポイントへのアクセスに使用する承認の種類 (`auth-mode: key`) を指定します。

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-sai-create-endpoint.yml":::

この YAML の例 (`2-sai-deployment.yml`) によって、以下が行われます。

* 作成するエンドポイントの種類が `online` であることを指定します。
* エンドポイントに、`blue` というデプロイが関連付けられるように指定します
* デプロイするモデル、使用する環境とスコアリング スクリプトなど、デプロイの詳細を構成します。

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-sai-deployment.yml":::

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

次の YAML の例は `endpoints/online/managed/managed-identities/1-uai-create-endpoint` にあります。 このファイルによって、以下が行われます。 

* エンドポイントを参照する際に使用する名前 (`my-uai-endpoint`) を定義します。
* エンドポイントへのアクセスに使用する承認の種類 (`auth-mode: key`) を指定します。
* 使用する id の種類 (`type: user_assigned`) を示します。

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/1-uai-create-endpoint.yml":::

この YAML の例 (`2-sai-deployment.yml`) によって、以下が行われます。

* 作成するエンドポイントの種類が `online` であることを指定します。
* エンドポイントに、`blue` というデプロイが関連付けられるように指定します
* デプロイするモデル、使用する環境とスコアリング スクリプトなど、デプロイの詳細を構成します。

:::code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/managed-identities/2-uai-deployment.yml":::

---

## <a name="configure-variables-for-deployment"></a>デプロイ用の変数を構成する

デプロイメントで使用するために作成するエンドポイント、ワークスペース、ワークスペースの場所について、それぞれの変数名を構成します。

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

次のコードは、これらの値をエンドポイントの環境変数としてエクスポートするものです。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="set_variables" :::

次に、BLOB ストレージ アカウント、BLOB コンテナー、ファイルに付けたい名前を指定します。 ここでこれらの変数名を定義しておいて、次のセクションの `az storage account create` コマンドや `az storage container create` コマンドから参照することになります。

次のコードは、それらの値を環境変数としてエクスポートするものです。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="configure_storage_names" :::

これらの変数をエクスポートした後、テキスト ファイルをローカルに作成します。 エンドポイントがデプロイされると、スコアリング スクリプトは、エンドポイントの作成時に生成されるシステム割り当てマネージド ID を使用して、このテキスト ファイルにアクセスします。

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

エンドポイント、ワークスペース、ワークスペースの場所の名前を決定し、その値を環境変数としてエクスポートします。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_variables" :::

次に、BLOB ストレージ アカウント、BLOB コンテナー、ファイルに付けたい名前を指定します。 ここでこれらの変数名を定義しておいて、次のセクションの `az storage account create` コマンドや `az storage container create` コマンドから参照することになります。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="configure_storage_names" :::

これらの変数をエクスポートした後、テキスト ファイルをローカルに作成します。 エンドポイントがデプロイされると、スコアリング スクリプトは、エンドポイントで使用されるシステム割り当てマネージド ID を使用して、このテキスト ファイルにアクセスします。 

ユーザー id 名の名前を決定し、その値を環境変数としてエクスポートします。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="set_user_identity_name" :::

---

## <a name="create-the-managed-identity"></a>マネージド ID を作成する 
Azure リソースにアクセスするには、エンドポイントに対するシステム割り当てマネージド ID またはユーザー割り当てマネージド ID を作成します。 

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

[オンラインエンドポイントを作成](#create-a-managed-online-endpoint)すると、システム割り当てマネージド ID が自動的に生成されるため、個別に作成する必要はありません。 

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

ユーザー割り当てマネージド ID を作成するには、次を使用します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_user_identity" :::

---

## <a name="create-storage-account-and-container"></a>Storage アカウントとコンテナーを作成する

この例では、BLOB ストレージ アカウントと BLOB コンテナーを作成し、先ほど作成したテキスト ファイルを BLOB コンテナーにアップロードします。 これは、エンドポイントとマネージド ID アクセスを付与するストレージ アカウントと BLOB コンテナーです。 

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

まず、ストレージ アカウントを作成します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_account" :::

次に、ストレージ アカウントに BLOB コンテナーを作成します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_storage_container" :::

その後、テキスト ファイルを BLOB コンテナーにアップロードします。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="upload_file_to_storage" :::

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

まず、ストレージ アカウントを作成します。  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_account" :::

次のものを使用して、既存のストレージ アカウント ID を取得することもできます。 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_storage_account_id" :::

次に、ストレージ アカウントに BLOB コンテナーを作成します。 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_storage_container" :::

次に、ファイルをコンテナーにアップロードします。 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="upload_file_to_storage" :::

---

## <a name="create-a-managed-online-endpoint"></a>マネージド オンライン エンドポイントを作成する

次のコードでは、デプロイを指定せずにマネージド オンライン エンドポイントを作成します。 

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)
マネージド エンドポイントを作成すると、そのシステム割り当てマネージド ID が既定で作成されます。

>[!IMPORTANT]
> システム割り当てマネージド ID は不変であり、作成後は変更できません。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="create_endpoint" :::

エンドポイントの状態を次のようにして確認します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_endpoint_Status" :::

問題が発生した場合は、「[マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)」を参照してください。

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

エンドポイントの状態を次のようにして確認します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

問題が発生した場合は、「[マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md)」を参照してください。

---

## <a name="give-access-permission-to-the-managed-identity"></a>マネージド ID にアクセス許可を付与する

>[!IMPORTANT] 
> オンライン エンドポイントでは、コンテナー レジストリーに対する Azure Container Registry のプルアクセス許可である AcrPull アクセス許可と、ワークスペースの既定データストアに対するストレージ BLOB データ閲覧者アクセス許可が必要です。

マネージド エンドポイント アクセス許可でシステム割り当てマネージド ID を使用してストレージにアクセスできるようにしたり、ユーザー割り当てマネージド ID に対するアクセス許可を付与して、前のセクションで作成されたストレージ アカウントにアクセスできるようにすることができます。

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

エンドポイントに対して作成されたシステム割り当てマネージド ID を取得します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="get_system_identity" :::

ここから、システム割り当てマネージド ID に、ご使用のストレージへのアクセス許可を与えることができます。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="give_permission_to_user_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

ユーザー割り当てマネージド ID のクライアント ID を取得します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_client_id" :::

ユーザー割り当てマネージド ID を取得します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_user_identity_id" :::

ワークスペースに関連付けられているコンテナー レジストリを取得します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_container_registry_id" :::

ワークスペースの既定のストレージを取得します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="get_workspace_storage_id" :::

ユーザー割り当てマネージド ID に、ストレージ アカウントのアクセス許可を付与します。  

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_user_storage_account" :::

ユーザー割り当てマネージド ID に、コンテナー レジストリのアクセス許可を付与します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_container_registry" :::

ユーザー割り当てマネージド ID に、既定のワークスペース ストレージのアクセス許可を付与します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="give_permission_to_workspace_storage_account" :::

---

## <a name="scoring-script-to-access-azure-resource"></a>Azure リソースにアクセスするためのスコアリング スクリプト

ID トークンを使用して Azure リソースにアクセスする方法を理解するには、次のスクリプトを参照してください。このシナリオでは、前のセクションで作成したストレージ アカウントです。 

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/onlinescoring/score_managedidentity.py":::

## <a name="create-a-deployment-with-your-configuration"></a>自分の構成を使用してデプロイを作成する

マネージド エンドポイントに関連付けられているデプロイを作成します。 [マネージド オンライン エンドポイントへのデプロイの詳細について学習します](how-to-deploy-managed-online-endpoints.md)。

>[!WARNING]
> 基になる環境とイメージが初めて構築されるものであるかどうかに応じて、このデプロイには 8 分から 14 分程度かかる場合があります。 その後、同じ環境を使用したデプロイは、それよりも短時間で完了します。

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="deploy" :::

>[!NOTE]
> `--name` 引数の値によって、YAML ファイル内の `name` キーがオーバーライドされる可能性があります。

デプロイの状態を確認します。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deploy_Status" :::

特定のデータのみが返されるように上記のクエリを調整する方法については、[Azure CLI コマンドの出力の照会](/cli/azure/query-azure-cli)に関するページを参照してください。

> [!NOTE]
> スコアリング スクリプトの init メソッドは、システム割り当てマネージド ID トークンを使用して、ストレージ アカウントからファイルを読み取ります。

init メソッドの出力をチェックするには、次のコードでデプロイ ログを確認します。 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="check_deployment_log" :::


# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="create_endpoint" :::

>[!Note]
> `--name` 引数の値によって、YAML ファイル内の `name` キーがオーバーライドされる可能性があります。

コマンドが実行されると、デプロイの状態を確認できます。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_endpoint_Status" :::

特定のデータのみが返されるように上記のクエリを調整する方法については、[Azure CLI コマンドの出力の照会](/cli/azure/query-azure-cli)に関するページを参照してください。

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

> [!NOTE]
> スコアリング スクリプトの init メソッドは、システム割り当てマネージド ID トークンを使用して、ストレージ アカウントからファイルを読み取ります。

init メソッドの出力をチェックするには、次のコードでデプロイ ログを確認します。 

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="check_deployment_log" :::

---

デプロイが完了すると、モデル、環境、エンドポイントが Azure Machine Learning ワークスペースに登録されます。

## <a name="confirm-your-endpoint-deployed-successfully"></a>エンドポイントが正しくデプロイされたことを確認する

エンドポイントがデプロイされた後、その操作を確認します。 推論の詳細は、モデルによって異なります。 このガイドでは、JSON クエリ パラメーターは次のようになります。 

:::code language="json" source="~/azureml-examples-cli-preview/cli/endpoints/online/model-1/sample-request.json" :::

エンドポイントを呼び出すには、次を実行します。

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="test_endpoint" :::

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="test_endpoint" :::

---

## <a name="delete-the-endpoint-and-storage-account"></a>エンドポイントとストレージ アカウントを削除する

デプロイしたエンドポイントとストレージを今後使用する予定がない場合は、コストを節約するためにそれらを削除してください。 エンドポイントを削除すると、そこに関連付けられているデプロイもすべて削除されます。

# <a name="system-assigned-managed-identity"></a>[システム割り当てマネージド ID](#tab/system-identity)
 
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-sai.sh" id="delete_storage_account" :::

# <a name="user-assigned-managed-identity"></a>[ユーザー割り当てマネージド ID](#tab/user-identity)

::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_endpoint" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_storage_account" :::
::: code language="azurecli" source="~/azureml-examples-cli-preview/cli/deploy-managed-online-endpoint-access-resource-uai.sh" id="delete_user_identity" :::

---

## <a name="next-steps"></a>次の手順

* [マネージド オンライン エンドポイントを使用して機械学習モデルをデプロイおよびスコアリングします (プレビュー)](how-to-deploy-managed-online-endpoints.md)。
* デプロイの詳細については、[オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md) に関するページを参照してください。
* CLI の使用について詳しくは、「[Azure Machine Learning 用の CLI 拡張機能を使用する](reference-azure-machine-learning-cli.md)」を参照してください。
* 使用できるコンピューティング リソースを確認するには、「[マネージド オンライン エンドポイント SKU の一覧 (プレビュー)](reference-managed-online-endpoints-vm-sku-list.md)」を参照してください。
* コストの詳細については、「[Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)」を参照してください。
* エンドポイントの監視については、「[マネージド オンライン エンドポイント (プレビュー) を監視する](how-to-monitor-online-endpoints.md)」を参照してください。
* マネージド エンドポイントの制限については、「[Azure Machine Learning のリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)」を参照してください。
