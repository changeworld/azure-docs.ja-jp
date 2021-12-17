---
title: 'クイックスタート: PowerShell または Azure CLI を使用して Purview アカウントを作成する'
description: このクイックスタートでは、Azure PowerShell または Azure CLI を使用して、Azure Purview アカウントを作成する方法について説明します。
author: hophanms
ms.author: hophan
ms.date: 10/28/2021
ms.topic: quickstart
ms.service: purview
ms.custom: mode-api, devx-track-azurepowershell
ms.openlocfilehash: ac06f6641f60ec1775e161ec1e949522321fd5af
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990199"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>クイックスタート: Azure PowerShell または Azure CLI を使用して Azure Purview アカウントを作成する

このクイックスタートでは、Azure PowerShell または Azure CLI を使用して、Azure Purview アカウントを作成します。 [Purview に関する PowerShell リファレンス](/powershell/module/az.purview/)を利用できますが、この記事では、PowerShell を使用してアカウントを作成するために必要なすべての手順について説明します。

Azure Purview は、データ環境の管理と制御に役立つデータ ガバナンス サービスです。 Purview では、オンプレミス、マルチクラウド、サービスとしてのソフトウェア (SaaS) のソース全体のデータに接続することで、情報の最新のマップが作成されます。 これにより、機密データが識別されて分類され、エンドツーエンドの系列が提供されます。 データ コンシューマーは組織全体のデータを検出でき、データ管理者はデータを監査し、セキュリティで保護し、データの適切な使用を確保することができます。

Purview の詳細については、[概要ページを参照してください](overview.md)。 組織全体に Purview をデプロイする方法の詳細については、[デプロイのベスト プラクティスを参照してください](deployment-best-practices.md)。

[!INCLUDE [purview-quickstart-prerequisites](includes/purview-quickstart-prerequisites.md)]

### <a name="install-powershell"></a>PowerShell をインストールする

 テンプレートをデプロイするクライアント マシンに Azure PowerShell または Azure CLI をインストールします ([コマンド ライン デプロイ](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment))。

## <a name="create-an-azure-purview-account"></a>Azure Purview アカウントを作成する

1. Azure の資格情報でサインインします

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Connect-AzAccount
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az login
    ```

    ---

1. 複数の Azure サブスクリプションがある場合は、使用するサブスクリプションを選択します。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```

    ---

1. Purview アカウント用のリソース グループを作成します。 既にお持ちの場合は、このステップをスキップしてかまいません。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name myResourceGroup -Location 'East US'
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```

    ---

1. Purview アカウントを作成またはデプロイします。

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    [New-AzPurviewAccount](/powershell/module/az.purview/new-azpurviewaccount) コマンドレットを使用して Purview アカウントを作成します。

    ```azurepowershell
    New-AzPurviewAccount -Name yourPurviewAccountName -ResourceGroupName myResourceGroup -Location eastus -IdentityType SystemAssigned -SkuCapacity 4 -SkuName Standard -PublicNetworkAccess
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    1. Purview テンプレート ファイルを作成します (`purviewtemplate.json` など)。 `name`、`location`、`capacity` (`4` または `16`) を更新できます。

        ```json
        {
          "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "resources": [
            {
              "name": "<yourPurviewAccountName>",
              "type": "Microsoft.Purview/accounts",
              "apiVersion": "2020-12-01-preview",
              "location": "EastUs",
              "identity": {
                "type": "SystemAssigned"
              },
              "properties": {
                "networkAcls": {
                  "defaultAction": "Allow"
                }
              },
              "dependsOn": [],
              "sku": {
                "name": "Standard",
                "capacity": "4"
              },
              "tags": {}
            }
          ],
          "outputs": {}
        }
        ```

    1. Purview テンプレートをデプロイする

        このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。

        ```azurecli
        az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
        ```

    ---

1. デプロイ コマンドから結果が返されます。 デプロイが成功したかどうかは、`ProvisioningState` を見て確認します。

1. ユーザー アカウントではなくサービス プリンシパルを使用して Azure Purview アカウントをデプロイした場合は、Azure CLI で次のコマンドも実行する必要があります。

    ```azurecli
    az purview account add-root-collection-admin --account-name --resource-group [--object-id]
    ```

    このコマンドを使用すると、Azure Purview アカウントのルート コレクションに対する[コレクション管理者](catalog-permissions.md#roles)権限がユーザー アカウントに付与されます。 これにより、ユーザーは Purview Studio にアクセスして、他のユーザーのアクセス許可を追加できます。 Azure Purview のアクセス許可の詳細については、[アクセス許可に関するガイド](catalog-permissions.md)を参照してください。 コレクションの詳細については、[コレクションの管理に関する記事](how-to-create-and-manage-collections.md)を参照してください。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Purview アカウントを作成する方法について学習しました。

次の記事に従い、Purview Studio を操作する方法、コレクションを作成する方法、Purview にアクセス権を付与する方法について学習してください。

* [Purview Studio の使用方法](use-purview-studio.md)
* [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
* [コレクションの作成](quickstart-create-collection.md)
