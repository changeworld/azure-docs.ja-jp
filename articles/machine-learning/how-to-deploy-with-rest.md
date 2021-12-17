---
title: REST API で管理されたオンライン エンドポイントを使用してモデルをデプロイする (プレビュー)
titleSuffix: Azure Machine Learning
description: REST API で管理されたオンライン エンドポイントを使用してモデルをデプロイする方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
author: rsethur
ms.author: seramasu
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devplatv2
ms.openlocfilehash: 724c7a3108f1b0bb699c81444a76e34d795f1705
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056135"
---
# <a name="deploy-models-with-rest-preview"></a>REST を使用してモデルをデプロイする (プレビュー)

Azure Machine Learning REST API を使用してモデルをデプロイする方法について学習します (プレビュー)。

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

## <a name="set-endpoint-name"></a>エンドポイント名を設定する

> [!NOTE]
> エンドポイントの名前は、Azure リージョン レベルで一意である必要があります。 たとえば、westus2 に存在できる my-endpoint という名前のエンドポイントは 1 つだけです。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="set_endpoint_name":::

## <a name="azure-machine-learning-managed-online-endpoints"></a>Azure Machine Learning マネージド オンライン エンドポイント (プレビュー)
マネージド オンライン エンドポイント (プレビュー) を使用すると、基盤となるインフラストラクチャを作成および管理することなくモデルをデプロイすることができます。 この記事では、オンライン エンドポイントとデプロイを作成し、それを呼び出して検証します。 ただし、まず、モデル、コード、環境など、デプロイに必要な資産を登録する必要があります。

Azure Machine Learning オンライン エンドポイントを作成するには、[Azure CLI を含め](how-to-deploy-managed-online-endpoints.md)、さまざまな方法があります。また、[Studio](how-to-use-managed-online-endpoint-studio.md) を使用して視覚的に作成することもできます。 次の例は、REST API を使用した、マネージド オンライン エンドポイントです。

## <a name="create-machine-learning-assets"></a>機械学習資産を作成する

最初に、Azure Machine Learning 資産を設定して、ジョブを構成します。

次の REST API 呼び出しでは、プレースホルダーとして `SUBSCRIPTION_ID`、`RESOURCE_GROUP`、`LOCATION`、および `WORKSPACE` を使用しています。 プレースホルダーを実際の値に置き換えてください。 

管理 REST により、[サービス プリンシパル認証トークン](how-to-manage-rest.md#retrieve-a-service-principal-authentication-token)が要求されます。 `TOKEN` は、実際の値に置き換えてください。 このトークンは、次のコマンドを使用して取得できます。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

サービス プロバイダーは、`api-version` 引数を使用して互換性を保証します。 `api-version` 引数はサービスによって異なります。 将来のバージョンに対応するために、API バージョンを変数として設定します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="api_version":::

### <a name="get-storage-account-details"></a>ストレージ アカウントの詳細を取得する

モデルとコードを登録するには、まず、それらをストレージ アカウントにアップロードする必要があります。 ストレージ アカウントの詳細は、データ ストアで使用できます。 この例では、自分のワークスペースの既定のデータストアと Azure ストレージ アカウントを取得します。 GET 要求を使用して自分のワークスペースに対してクエリを実行し、情報が含まれた JSON ファイルを取得します。

[jq](https://stedolan.github.io/jq/) ツールを使用して、JSON の結果を解析し、必要な値を取得できます。 Azure portal を使用しても同じ情報を取得することができます。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_storage_details":::

### <a name="upload--register-code"></a>コードをアップロードして登録する

データストアを用意できたら、スコアリング スクリプトをアップロードできます。 Azure Storage CLI を使用して、BLOB を既定のコンテナーにアップロードします。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_code":::

> [!TIP]
> また、Azure portal や [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) など、他の方法でアップロードすることもできます。

コードをアップロードしたら、PUT 要求を使用してコードを指定し、`datastoreId` を使用してデータストアを参照することができます。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_code":::

### <a name="upload-and-register-model"></a>モデルをアップロードして登録する

コードと同様に、モデル ファイルをアップロードします。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="upload_model":::

モデルを登録します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_model":::

### <a name="create-environment"></a>環境を作成する
デプロイは、必要な依存関係がある環境で実行する必要があります。 PUT 要求を使用して環境を作成します。 Microsoft Container Registry の Docker イメージを使用します。 `Docker` を使用して Docker イメージを構成し、`condaFile` を使用して conda の依存関係を追加できます。

次のスニペットでは、Conda 環境 (YAML ファイル) の内容が環境変数に読み込まれています。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_environment":::

### <a name="create-endpoint"></a>エンドポイントを作成する

オンライン エンドポイントを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_endpoint":::

### <a name="create-deployment"></a>Create deployment

エンドポイントでデプロイを作成します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="create_deployment":::

### <a name="invoke-the-endpoint-to-score-data-with-your-model"></a>エンドポイントを呼び出し、モデルを使用してデータをスコアリングする

エンドポイントを呼び出すには、スコアリング URI とアクセス トークンが必要です。 最初にスコアリング URI を取得します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_endpoint":::

エンドポイントのアクセス トークンを取得します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_access_token":::

次に、curl を使用してエンドポイントを呼び出します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="score_endpoint":::

### <a name="check-the-logs"></a>ログを確認する

デプロイ ログを確認します。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="get_deployment_logs":::

### <a name="delete-the-endpoint"></a>エンティティを削除する

今後使用する予定がない場合、以下のコマンドでデプロイを削除してください (エンドポイントとそこにあるすべてのデプロイが削除されます)。

:::code language="rest-api" source="~/azureml-examples-main/cli/deploy-rest.sh" id="delete_endpoint":::

## <a name="next-steps"></a>次の手順

* [Azure CLI を使用して](how-to-deploy-managed-online-endpoints.md)モデルをデプロイする方法について説明します。
* [Studio を使用して](how-to-use-managed-online-endpoint-studio.md)モデルをデプロイする方法について説明します。
* [マネージド オンライン エンドポイントのデプロイとスコアリングのトラブルシューティング (プレビュー)](how-to-troubleshoot-managed-online-endpoints.md) について説明します
* [マネージド オンライン エンドポイントとマネージド ID (プレビュー) を使用して Azure リソースにアクセスする](how-to-access-resources-from-endpoints-managed-identities.md)方法について説明します
* [オンライン エンドポイントを監視する](how-to-monitor-online-endpoints.md)方法について説明します。
* [オンライン エンドポイントの安全なロールアウト (プレビュー)](how-to-safely-rollout-managed-endpoints.md) について説明します。
* [Azure Machine Learning のマネージド オンライン エンドポイント (プレビュー) のコストを表示する](how-to-view-online-endpoints-costs.md)。
* [マネージド オンライン エンドポイント SKU の一覧 (プレビュー)](reference-managed-online-endpoints-vm-sku-list.md)。
* マネージド オンライン エンドポイントに関する制限の詳細については、「[Azure Machine Learning を使用するリソースのクォータの管理と引き上げ](how-to-manage-quotas.md#azure-machine-learning-managed-online-endpoints-preview)」を参照してください。