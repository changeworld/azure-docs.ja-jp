---
title: Terraform を使用してワークスペースを管理する
titleSuffix: Azure Machine Learning
description: Terraform を使用して Azure Machine Learning ワークスペースを管理する方法について説明します。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: deeikele
author: denniseik
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: ''
ms.openlocfilehash: 21e3f646f384695fa6a305eb45f7e3bb019a18e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2021
ms.locfileid: "131560817"
---
# <a name="manage-azure-machine-learning-workspaces-using-terraform-preview"></a>Terraform を使用して Azure Machine Learning ワークスペースを管理する (プレビュー)

この記事では、Terraform の構成ファイルを使用して、Azure Machine Learning ワークスペースを作成して管理する方法について説明します。 [Terraform](/azure/developer/terraform/) のテンプレート ベースの構成ファイルを使用すると、Azure リソースの定義、作成、構成を反復的および計画的に行うことができます。 Terraform によって、リソースの状態が追跡され、リソースをクリーンアップおよび破棄できます。 

Terraform の構成は、デプロイに必要なリソースが定義されているドキュメントです。 デプロイ変数を指定することもできます。 変数は、構成の使用時に入力値を指定するために使用されます。

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://azure.microsoft.com/free/) をお試しください。
* [Azure CLI](/cli/azure/) のインストールされているバージョン。
* Terraform の構成: この記事および[Terraform と Azure へのアクセスの構成](/azure/developer/terraform/get-started-cloud-shell)に関する記事の手順に従ってください。

## <a name="declare-the-azure-provider"></a>Azure プロバイダーを宣言する

Azure プロバイダーを宣言する Terraform 構成ファイルを作成します。

1. `main.tf` という名前で新しいファイルを作成します。 Azure Cloud Shell で作業している場合は、bash を使用します。

    ```bash
    code main.tf
    ```

1. 以下のコードをエディターに貼り付けます。

    **main.tf**:
    :::code language="terraform" source="~/terraform/quickstart/101-machine-learning/main.tf":::

1. ファイルを保存し ( **&lt;Ctrl> + S** キー)、エディターを終了します ( **&lt;Ctrl> + Q** キー)。

## <a name="deploy-a-workspace"></a>ワークスペースをデプロイする

Azure Machine Learning ワークスペースを作成するには、次の Terraform 構成を使用できます。 Azure Machine Learning ワークスペースを作成するときは、依存関係として他のさまざまなサービスが必要です。 テンプレートでは、これらの[ワークスペースに関連付けられているリソース](./concept-workspace.md#resources)も指定します。 ニーズに応じて、パブリックまたはプライベート ネットワーク接続を使用するリソースを作成するテンプレートを使用できます。

# <a name="public-network-connectivity"></a>[パブリック ネットワーク接続](#tab/publicworkspace)

Azure の一部のリソースには、グローバルに一意の名前が必要です。 次のテンプレートを使用してリソースをデプロイする前に、`name` 変数に一意の値を設定します。

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/101-machine-learning/workspace.tf":::

# <a name="private-network-connectivity"></a>[プライベート ネットワーク接続](#tab/privateworkspace)

次の構成を使用すると、Azure Private Link エンドポイントを使用して分離されたネットワーク環境にワークスペースが作成されます。 [プライベート DNS ゾーン](../dns/private-dns-privatednszone.md)が含まれているので、仮想ネットワーク内でドメイン名を解決できます。

Azure の一部のリソースには、グローバルに一意の名前が必要です。 次のテンプレートを使用してリソースをデプロイする前に、`resourceprefix` 変数に一意の値を設定します。

Azure Container Registry と Azure Machine Learning の両方にプライベート リンク エンドポイントを使用すると、[環境](/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py&preserve-view=true)イメージのビルドに Azure Container Registry タスクを使用できません。 代わりに、Azure Machine Learning コンピューティング クラスターを使用してイメージをビルドできます。 使用するクラスター名を構成するには、[image_build_compute_name](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 引数を設定します。 [public_network_access_enabled](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs/resources/machine_learning_workspace) 引数を使用すると、プライベート リンク エンドポイントを持つワークスペースへの[パブリック アクセスを許可する](./how-to-configure-private-link.md?tabs=python#enable-public-access)ように構成できます。

**variables.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/variables.tf":::

**workspace.tf**:
:::code language="terraform" source="~/terraform/quickstart/201-machine-learning-moderately-secure/workspace.tf":::

**network.tf**:
```terraform
# Virtual Network
resource "azurerm_virtual_network" "default" {
  name                = "vnet-${var.name}-${var.environment}"
  address_space       = var.vnet_address_space
  location            = azurerm_resource_group.default.location
  resource_group_name = azurerm_resource_group.default.name
}

resource "azurerm_subnet" "snet-training" {
  name                                           = "snet-training"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.training_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-aks" {
  name                                           = "snet-aks"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.aks_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_subnet" "snet-workspace" {
  name                                           = "snet-workspace"
  resource_group_name                            = azurerm_resource_group.default.name
  virtual_network_name                           = azurerm_virtual_network.default.name
  address_prefixes                               = var.ml_subnet_address_space
  enforce_private_link_endpoint_network_policies = true
}

# ...
# For full reference, see: https://github.com/Azure/terraform/blob/master/quickstart/201-machine-learning-moderately-secure/network.tf
```

プライベート リンク エンドポイント ワークスペースに接続するには、複数のオプションがあります。 これらのオプションの詳細については、「[ワークスペースに安全に接続する](./how-to-secure-workspace-vnet.md#securely-connect-to-your-workspace)」を参照してください。

---

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="resource-provider-errors"></a>リソース プロバイダーのエラー

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="current-limitations"></a>現在の制限

* 現時点では、カスタマー マネージド暗号化キーを使用するワークスペースのデプロイに Terraform を使用することはできません。

## <a name="next-steps"></a>次の手順

* Azure での Terraform のサポートの詳細については、「[Azure 上の Terraform のドキュメント](/azure/developer/terraform/)」を参照してください。
* Terraform の "クイックスタート" テンプレートの例を見つけるには、「[Azure Terraform QuickStart Templates (Azure Terraform クイックスタート テンプレート)](https://github.com/Azure/terraform/tree/master/quickstart)」を参照してください。
  
  * [101: Machine Learning ワークスペースとコンピューティング](https://github.com/Azure/terraform/tree/master/quickstart/101-machine-learning) – Azure ML を使い始めるために必要な最小限のリソースのセット。
  * [201: Machine Learning ワークスペース、コンピューティング、ネットワーク分離のためのネットワーク コンポーネントのセット](https://github.com/Azure/terraform/tree/master/quickstart/201-machine-learning-moderately-secure) – HBI データで使用する運用パイロット環境を作成するために必要なすべてのリソース。
  * [202: 201 に似ているが、既存のネットワーク コンポーネントを使用するオプション](https://github.com/Azure/terraform/tree/master/quickstart/202-machine-learning-moderately-secure-existing-VNet)。
  
* Terraform Azure プロバイダーの詳細については、[Terraform レジストリの Azure Resource Manager プロバイダー](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)に関するページを参照してください。
* ネットワーク構成オプションの詳細については、「[仮想ネットワーク (VNet) を使用して Azure Machine Learning ワークスペース リソースを保護する](./how-to-network-security-overview.md)」を参照してください。
* Azure Resource Manager テンプレート ベースの別のデプロイについては、[Resource Manager テンプレートと Resource Manager REST API を使用したリソースのデプロイ](../azure-resource-manager/templates/deploy-rest.md)に関する記事を参照してください。