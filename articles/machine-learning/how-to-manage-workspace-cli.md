---
title: Azure CLI を使用したワークスペースの作成
titleSuffix: Azure Machine Learning
description: 機械学習のための Azure CLI 拡張機能を使用して、新しい Azure Machine Learning ワークスペースを作成する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: a258a84930201a48aa70577d1349104b9cd6a480
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132723496"
---
# <a name="manage-azure-machine-learning-workspaces-using-azure-cli"></a>Azure CLI を使用して Azure Machine Learning ワークスペースを管理する

この記事では、Azure CLI を使用して Azure Machine Learning ワークスペースを作成および管理する方法について説明します。 Azure CLI には、Azure リソースを管理するためのコマンドが用意されています。また、オートメーションに重点を置くことで、Azure で迅速に作業できるように設計されています。 CLI の機械学習拡張機能には、Azure Machine Learning リソースを操作するためのコマンドが用意されています。

> [!NOTE]
> この記事の例では、1.0 CLI と 2.0 CLI の両方のバージョンを参照します。 バージョンの指定がないコマンドは、1.0 と 2.0 CLI のどちらでも実行できます。 機械学習 2.0 CLI は現在、パブリック プレビューの段階です。 このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。

* ご使用の **ローカル環境** からこのドキュメントの CLI コマンドを使用するには、[Azure CLI](/cli/azure/install-azure-cli) が必要です。

    [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/) を使用する場合は、ブラウザーを使用してクラウド内に存在する CLI にアクセスします。

## <a name="limitations"></a>制限事項

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

## <a name="connect-the-cli-to-your-azure-subscription"></a>Azure サブスクリプションへの CLI の接続

> [!IMPORTANT]
> Azure Cloud Shell を使用している場合は、このセクションを省略できます。 Cloud Shell では、Azure サブスクリプションにログインするアカウントを使用して自動的に認証が行われます。

CLI から Azure サブスクリプションを認証するには、いくつかの方法があります。 最も簡単な方法は、ブラウザーを使用して対話形式で認証することです。 対話形式で認証するには、コマンド ラインまたはターミナルを開き、次のコマンドを使用します。

```azurecli-interactive
az login
```

CLI で既定のブラウザーを開くことができる場合、開いたブラウザにサインイン ページが読み込まれます。 それ以外の場合は、ブラウザーを開き、コマンド ラインの指示に従う必要があります。 この手順では、[https://aka.ms/devicelogin](https://aka.ms/devicelogin) にアクセスして認証コードを入力する必要があります。

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

その他の認証方法については、「[Azure CLI を使用してサインインする](/cli/azure/authenticate-azure-cli)」を参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

Azure Machine Learning ワークスペースは、リソース グループ内に作成する必要があります。 既存のリソース グループを使用することも、新しいリソース グループを作成することもできます。 __新しいリソース グループを作成__ するには、次のコマンドを使用します。 `<resource-group-name>` をこのリソース グループに使用する名前に置き換えます。 `<location>` をこのリソース グループに使用する Azure リージョンに置き換えます。

> [!NOTE]
> Azure Machine Learning が使用可能なリージョンを選択する必要があります。 詳細については、「[リージョン別の利用可能な製品](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)」を参照してください。

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

このコマンドからの応答は、次の JSON のようになります。 出力値を使用して、作成されたリソースを特定したり、オートメーションのための後続の CLI ステップへの入力として解析したりすることができます。

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

リソース グループの操作の詳細については、「[az group](/cli/azure/group)」を参照してください。

## <a name="create-a-workspace"></a>ワークスペースの作成

Azure Machine Learning ワークスペースをデプロイするとき、他のさまざまなサービスが、[関連付けられている依存リソースとして必要](./concept-workspace.md#resources)です。 CLI を使用してワークスペースを作成するとき、CLI によって新しい関連リソースが作成されるようにすることも、自分で既存のリソースをアタッチすることもできます。

> [!IMPORTANT]
> 独自のストレージ アカウントをアタッチする場合は、次の条件を満たしていることを確認してください。
>
> * ストレージ アカウントが Premium アカウント (Premium_LRS と Premium_GRS) "_ではない_"
> * Azure Blob と Azure File の両方の機能が有効になっている
> * 階層型名前空間 (ADLS Gen 2) が無効です。これらの要件は、ワークスペースによって使用される "_既定_" のストレージ アカウントのみを対象としています。
>
> Azure コンテナー レジストリをアタッチするとき、これを Azure Machine Learning ワークスペースで使用するには、事前に[管理者アカウント](../container-registry/container-registry-authentication.md#admin-account)が有効になっている必要があります。

# <a name="create-with-new-resources"></a>[新しいリソースを使用して作成する](#tab/createnewresources)

__サービスが自動的に作成__ される新しいワークスペースを作成するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

# <a name="bring-existing-resources-10-cli"></a>[既存のリソースを持ち込む (1.0 CLI)](#tab/bringexistingresources1)
既存のリソースを使用するワークスペースを作成するには、各リソースのリソース ID を指定する必要があります。 この ID は、Azure portal を使用して各リソースの [プロパティ] タブを使用するか、Azure CLI を使用して次のコマンドを実行して取得できます。

  * **Azure Storage アカウント**:     `az storage account show --name <storage-account-name> --query "id"`
  * **Azure Application Insights**:     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
  * **Azure Key Vault**:     `az keyvault show --name <key-vault-name> --query "ID"`
  * **Azure Container Registry**:     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

  返されるリソース ID の形式は次のとおりです: `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"`。

ワークスペースで使用するリソースの ID を取得したら、ベース コマンド `az workspace create -w <workspace-name> -g <resource-group-name>` を使用して、既存のリソースのパラメーターと ID を追加します。 たとえば、次のコマンドでは、既存のコンテナー レジストリを使用するワークスペースが作成されます。

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

# <a name="bring-existing-resources-20-cli---preview"></a>[既存のリソースを持ち込む (2.0 CLI - プレビュー)](#tab/bringexistingresources2)

CLI を使用して既存の関連リソースを取り込むときに新しいワークスペースを作成するには、まず、構成ファイル内でワークスペースを構成する方法を定義する必要があります。

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/with-existing-resources.yml":::

その後、この構成ファイルを、ワークスペース作成 CLI コマンドの一部として参照できます。

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file workspace.yml
```

既存のリソースをアタッチするには、そのリソースの ID を指定する必要があります。 この ID は、Azure portal の各リソースの [プロパティ] タブを使用するか、次のコマンドを使用して Azure CLIだ。

* **Azure Storage アカウント**:     `az storage account show --name <storage-account-name> --query "id"`
* **Azure Application Insights**:     `az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"`
* **Azure Key Vault**:     `az keyvault show --name <key-vault-name> --query "ID"`
* **Azure Container Registry**:     `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

リソース ID の値は `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/<provider>/<subresource>/<resource-name>"` のようになります。

---

> [!IMPORTANT]
> 既存のリソースをアタッチする場合、必ずしもすべてを指定する必要はありません。 1 つ以上を指定できます。 たとえば、既存のストレージ アカウントを指定すると、ワークスペースによって他のリソースが作成されます。

ワークスペース作成コマンドの出力は、次の JSON のようになります。 出力値を使用して、作成されたリソースを特定したり、後続の CLI ステップへの入力として解析したりすることができます。

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}

```

## <a name="advanced-configurations"></a>詳細な構成
### <a name="configure-workspace-for-private-network-connectivity"></a>プライベート ネットワーク接続用にワークスペースを構成する

ユース ケースと組織の要件に応じて、プライベート ネットワーク接続を使用してAzure Machine Learning を構成できます。 Azure CLI を使用して、ワークスペースと、ワークスペース リソースのプライベート リンク エンドポイントをデプロイできます。 お使いのワークスペースでのプライベート エンドポイントと仮想ネットワーク (VNet) の使用の詳細については、「[仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)」を参照してください。 複雑なリソース構成については、[Azure Resource Manager](how-to-create-workspace-template.md) などのテンプレート ベースのデプロイ オプションも参照してください。

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

ワークスペースへのアクセスを仮想ネットワークに制限する場合、`az ml workspace create` コマンドの一部として次のパラメーターを使用するか、`az ml workspace private-endpoint` を使用することができます。

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --pe-name "<pe name>"
                       --pe-auto-approval "<pe-autoapproval>"
                       --pe-resource-group "<pe name>"
                       --pe-vnet-name "<pe name>"
                       --pe-subnet-name "<pe name>"
```

* `--pe-name`:作成するプライベート エンドポイントの名前。
* `--pe-auto-approval`:ワークスペースへのプライベート エンドポイント接続を自動的に承認するかどうか。
* `--pe-resource-group`:プライベート エンドポイントを作成するリソース グループ。 仮想ネットワークが含まれているグループと同じグループでなければなりません。
* `--pe-vnet-name`:プライベート エンドポイントを作成する既存の仮想ネットワーク。
* `--pe-subnet-name`:プライベート エンドポイントを作成するサブネットの名前。 既定値は `default` です。

これらのコマンドの使用方法の詳細については、[CLI リファレンス ページ](/cli/azure/ml(v1)/workspace)をご覧ください。

# <a name="20-cli---preview"></a>[2.0 CLI - プレビュー](#tab/vnetpleconfigurationsv2cli)

プライベート リンクを使用しているときは、ワークスペースで、Azure Container Registry タスク コンピューティングを使用してイメージを作成することができません。 そのため、image_build_compute プロパティに CPU コンピューティング クラスター名を設定して、Docker イメージの環境作成に使用できるようにする必要があります。 インターネットでプライベート リンク ワークスペースにアクセスできるようにするかどうかも、public_network_access プロパティによって指定できます。

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/privatelink.yml":::

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file privatelink.yml
```

ワークスペースの作成後、[Azure ネットワーク CLI コマンド](/cli/azure/network/private-endpoint#az_network_private_endpoint_create)で、ワークスペースのプライベート リンク エンドポイントを作成します。

```azurecli-interactive
az network private-endpoint create \
    --name <private-endpoint-name> \
    --vnet-name <vnet-name> \
    --subnet <subnet-name> \
    --private-connection-resource-id "/subscriptions/<subscription>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>" \
    --group-id amlworkspace \
    --connection-name workspace -l <location>
```

ワークスペースのプライベート DNS ゾーン エントリを作成するには、次のコマンドを使用します。

```azurecli-interactive
# Add privatelink.api.azureml.ms
az network private-dns zone create \
    -g <resource-group-name> \
    --name 'privatelink.api.azureml.ms'

az network private-dns link vnet create \
    -g <resource-group-name> \
    --zone-name 'privatelink.api.azureml.ms' \
    --name <link-name> \
    --virtual-network <vnet-name> \
    --registration-enabled false

az network private-endpoint dns-zone-group create \
    -g <resource-group-name> \
    --endpoint-name <private-endpoint-name> \
    --name myzonegroup \
    --private-dns-zone 'privatelink.api.azureml.ms' \
    --zone-name 'privatelink.api.azureml.ms'

# Add privatelink.notebooks.azure.net
az network private-dns zone create \
    -g <resource-group-name> \
    --name 'privatelink.notebooks.azure.net'

az network private-dns link vnet create \
    -g <resource-group-name> \
    --zone-name 'privatelink.notebooks.azure.net' \
    --name <link-name> \
    --virtual-network <vnet-name> \
    --registration-enabled false

az network private-endpoint dns-zone-group add \
    -g <resource-group-name> \
    --endpoint-name <private-endpoint-name> \
    --name myzonegroup \
    --private-dns-zone 'privatelink.notebooks.azure.net' \
    --zone-name 'privatelink.notebooks.azure.net'
```

---

### <a name="customer-managed-key-and-high-business-impact-workspace"></a>カスタマー マネージド キーと High Business Impact ワークスペース

ワークスペースのメタデータは、既定で Microsoft が管理する Azure Cosmos DB インスタンスに格納されます。 このデータは Microsoft のマネージド キーで暗号化されます。 Microsoft のマネージド キーを使用する代わりに、独自のキーを指定することもできます。 これにより、ご自身の Azure サブスクリプション内に、お使いのデータを格納するための追加のリソース セットが作成されます。

暗号化用の独自のキーを持ち込むときに作成されるリソースの詳細については、[Azure Machine Learning を使用したデータの暗号化](./concept-data-encryption.md#azure-cosmos-db)に関するページをご覧ください。

以下の CLI コマンドは、暗号化にカスタマー マネージド キーを使用するワークスペースを、1.0 CLI バージョンおよび 2.0 CLI バージョンを使って作成する例を示しています。

# <a name="10-cli"></a>[1.0 CLI](#tab/vnetpleconfigurationsv1cli)

`--cmk-keyvault` パラメーターを使用して、キーを格納する Azure Key Vault を指定し、`--resource-cmk-uri` を使用して、コンテナー内のキーのリソース ID と URI を指定します。

お使いのワークスペース上で [Microsoft が収集するデータを制限](./concept-data-encryption.md#encryption-at-rest)するには、追加で `--hbi-workspace` パラメーターを指定できます。 

```azurecli-interactive
az ml workspace create -w <workspace-name>
                       -g <resource-group-name>
                       --cmk-keyvault "<cmk keyvault name>"
                       --resource-cmk-uri "<resource cmk uri>"
                       --hbi-workspace
```

# <a name="20-cli---preview"></a>[2.0 CLI - プレビュー](#tab/vnetpleconfigurationsv2cli)

`customer_managed_key` パラメーターと、それに含まれる `key_vault` パラメーターおよび `key_uri` パラメータを使って、コンテナー内のキーのリソース ID と URI を指定します。

お使いのワークスペース上で [Microsoft が収集するデータを制限](./concept-data-encryption.md#encryption-at-rest)するには、追加で `hbi_workspace` プロパティを指定できます。 

:::code language="YAML" source="~/azureml-examples-main/cli/resources/workspace/cmk.yml":::

その後、この構成ファイルを、ワークスペース作成 CLI コマンドの一部として参照できます。

```azurecli-interactive
az ml workspace create -g <resource-group-name> --file cmk.yml
```
---

> [!NOTE]
> データ暗号化の追加リソースを管理するために、ご自分のサブスクリプションに対する共同作成者のアクセス許可を使用して、__Machine Learning アプリ__ (ID とアクセスの管理) を承認します。

> [!NOTE]
> Azure Cosmos DB は、モデルのパフォーマンス、実験によってログに記録された情報、またはモデル デプロイからログに記録された情報などの情報を格納するために使用されることは __ありません__。 これらの項目の監視の詳細については、アーキテクチャと概念に関する記事の「[監視とログ記録](concept-azure-machine-learning-architecture.md)」セクションを参照してください。

> [!IMPORTANT]
> High Business Impact の選択は、ワークスペースの作成時にのみ実行できます。 ワークスペースの作成後にこの設定を変更することはできません。

カスタマー マネージド キーと High Business Impact ワークスペースの詳細については、「[Azure Machine Learning のエンタープライズ セキュリティ](concept-data-encryption.md#encryption-at-rest)」を参照してください。

## <a name="using-the-cli-to-manage-workspaces"></a>CLI を使用したワークスペースの管理

### <a name="get-workspace-information"></a>ワークスペース情報の取得

ワークスペースに関する情報を取得するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

詳細については、[az ml workspace show](/cli/azure/ml/workspace#az_ml_workspace_show) のドキュメントをご覧ください。

### <a name="update-a-workspace"></a>ワークスペースの更新

ワークスペースを更新するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

詳細については、「[az ml workspace update](/cli/azure/ml/workspace#az_ml_workspace_update)」のドキュメントをご覧ください。

### <a name="sync-keys-for-dependent-resources"></a>依存リソースのキーの同期

ワークスペースに使用されているいずれかのリソースのアクセス キーを変更すると、ワークスペースが新しいキーと同期されるまでに約 1 時間かかります。 ワークスペースで新しいキーを直ちに同期させるには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

キーの変更の詳細については、「[ストレージ アカウント キーの再生成](how-to-change-storage-access-key.md)」を参照してください。

sync-keys コマンドの詳細については、[az ml workspace sync-keys](/cli/azure/ml/workspace#az_ml_workspace_sync-keys) に関するページをご覧ください。

### <a name="delete-a-workspace"></a>ワークスペースを削除する

[!INCLUDE [machine-learning-delete-workspace](../../includes/machine-learning-delete-workspace.md)]

不要になったワークスペースを削除するには、次のコマンドを使用します。

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> ワークスペースを削除しても、ワークスペースで使用されている Application Insights、ストレージ アカウント、キー コンテナー、またはコンテナー レジストリは削除されません。

リソース グループを削除することもできます。これにより、ワークスペースとリソース グループ内の他のすべての Azure リソースが削除されます。 リソース グループを削除するには、次のコマンドを使用します。

```azurecli-interactive
az group delete -g <resource-group-name>
```

詳細については、[az ml workspace delete](/cli/azure/ml/workspace#az_ml_workspace_delete) のドキュメントをご覧ください。

ワークスペースを誤って削除した場合でも、 ノートブックを取得できます。 [このドキュメント](./how-to-high-availability-machine-learning.md#workspace-deletion)を 参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>ワークスペースの移動

> [!WARNING]
> Azure Machine Learning ワークスペースを別のサブスクリプションに移動したり、所有するサブスクリプションを新しいテナントに移動したりすることは、サポートされていません。 エラーの原因になります。

### <a name="deleting-the-azure-container-registry"></a>Azure Container Registry の削除

Azure Machine Learning ワークスペースでは、一部の操作に対して Azure Container Registry (ACR) が使用されます。 これにより ACR インスタンスは、最初に必要になったときに自動的に作成されます。

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>次のステップ

機械学習の Azure CLI 拡張機能の詳細については、[az ml](/cli/azure/ml) のドキュメントを参照してください。