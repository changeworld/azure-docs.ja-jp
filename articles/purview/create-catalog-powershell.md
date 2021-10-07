---
title: 'クイックスタート: PowerShell または Azure CLI を使用して Purview アカウントを作成する'
description: このクイックスタートでは、Azure PowerShell または Azure CLI を使用して、Azure Purview アカウントを作成する方法について説明します。
author: hophanms
ms.author: hophan
ms.date: 09/27/2021
ms.topic: quickstart
ms.service: purview
ms.custom:
- mode-api
ms.openlocfilehash: 0e3cb8399e42dc651a48ada6018c58cb4f48e5d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217355"
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
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    このデプロイ コマンドを実行するには、[最新バージョン](/cli/azure/install-azure-cli)の Azure CLI が必要です。
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. デプロイ コマンドから結果が返されます。 デプロイが成功したかどうかは、`ProvisioningState` を見て確認します。
    
## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Purview アカウントを作成する方法について学習しました。

次の記事に従い、Purview Studio を操作する方法、コレクションを作成する方法、Purview にアクセス権を付与する方法について学習してください。

* [Purview Studio の使用方法](use-purview-studio.md)
* [コレクションの作成](quickstart-create-collection.md)
* [Azure Purview アカウントにユーザーを追加する](catalog-permissions.md)
