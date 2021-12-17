---
title: Azure Cosmos DB での継続的バックアップとポイントインタイム リストアを使用したアカウントのプロビジョニング
description: Azure portal、PowerShell、CLI、および Resource Manager テンプレートを使用して、継続的バックアップとポイントインタイム リストアを使用してアカウントをプロビジョニングする方法について説明します。
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/29/2021
ms.author: govindk
ms.reviewer: sngun
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 78996e58111d380778e8d02673f518720250e6fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781214"
---
# <a name="provision-an-azure-cosmos-db-account-with-continuous-backup-and-point-in-time-restore"></a>継続的バックアップとポイントインタイム リストアを使用して Azure Cosmos DB アカウントをプロビジョニングする 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB のポイントインタイム リストア機能を使用すると、コンテナー内で誤って行われた変更から回復し、削除されたアカウント、データベース、またはコンテナーの復元や、任意のリージョン (バックアップが存在していた場所) への復元ができます。 継続的バックアップ モードを使用すると、過去 30 日以内の任意の時点に復元できます。

この記事では、[Azure portal](#provision-portal)、[PowerShell](#provision-powershell)、[CLI](#provision-cli)、および [Resource Manager テンプレート](#provision-arm-template)を使用して、継続的バックアップとポイントインタイム リストアを使用してアカウントをプロビジョニングする方法について説明します。

> [!NOTE]
> 継続的バックアップ モードのアカウントは、次の条件に該当する場合にのみプロビジョニングできます。
>
> * アカウントの種類が SQL API または MongoDB の API の場合。
> * アカウントに単一の書き込みリージョンがある場合。
> * アカウントがカスタマー マネージド キー (CMK) で有効になっていない場合。

## <a name="provision-using-azure-portal"></a><a id="provision-portal">Microsoft Azure portal を使用したプロビジョニング</a>

新しい Azure Cosmos DB アカウントを作成するときは、 **[バックアップ ポリシー]** タブで **[継続的]** モードを選択し、新しいアカウントでポイントインタイム リストア機能を有効にします。 ポイントインタイム リストアでは、データは新しいアカウントに復元されます。現在、既存のアカウントに復元することはできません。

:::image type="content" source="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png" alt-text="継続的バックアップ構成を使用して Azure Cosmos DB アカウントをプロビジョニングします。" border="true" lightbox="./media/provision-account-continuous-backup/configure-continuous-backup-portal.png":::

## <a name="provision-using-azure-powershell"></a><a id="provision-powershell">Azure PowerShell を使用してプロビジョニングする</a>

アカウントをプロビジョニングする前に、[最新バージョンの Azure PowerShell](/powershell/azure/install-az-ps?view=azps-6.2.1&preserve-view=true)、または 6.2.0 より上位のバージョンをインストールしてください。 次に、Azure アカウントに接続し、次のコマンドを使用して必要なサブスクリプションを選択します。

1. 次のコマンドを使用して、Azure にサインインします。

   ```azurepowershell
   Connect-AzAccount
   ```

1. 次のコマンドを使用して、特定のサブスクリプションを選択します。

   ```azurepowershell
   Select-AzSubscription -Subscription <SubscriptionName>
   ```

#### <a name="sql-api-account"></a><a id="provision-powershell-sql-api"></a>SQL API アカウント

継続的バックアップを行うアカウントをプロビジョニングするには、通常のプロビジョニング コマンドに引数 `-BackupPolicyType Continuous` を追加します。

次のコマンドレットは、*MyRG* リソース グループで "*米国西部*" リージョンに作成された継続的バックアップ ポリシーを持つ単一リージョン書き込みアカウント *Pitracct* の例です。

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "pitracct" `
  -ApiKind "Sql"
      
```

#### <a name="api-for-mongodb"></a><a id="provision-powershell-mongodb-api"></a>MongoDB 用 API

次のコマンドレットは、*MyRG* リソース グループで "*米国西部*" リージョンに作成された継続的バックアップ アカウント *Pitracct* の例です。

```azurepowershell

New-AzCosmosDBAccount `
  -ResourceGroupName "MyRG" `
  -Location "West US" `
  -BackupPolicyType Continuous `
  -Name "Pitracct" `
  -ApiKind "MongoDB" `
  -ServerVersion "3.6"

```

## <a name="provision-using-azure-cli"></a><a id="provision-cli"></a>Azure CLI を使用したプロビジョニング

アカウントをプロビジョニングする前に、次の手順に従って Azure コマンド ライン インターフェイスをインストールします。

1. Azure CLI の最新バージョンをインストールします

   * [Azure コマンド ライン インターフェイス](/cli/azure/install-azure-cli)の最新バージョンまたは 2.26.0 より上位のバージョンをインストールします。
   * CLI を既にインストールしている場合は、`az upgrade` コマンドを使用して、最新バージョンに更新します。 このコマンドは、2.11 より上位の CLI バージョンでのみ機能します。 以前のバージョンがある場合は、上記のリンクを使用して最新バージョンをインストールします。

1. サインインして、使用するサブスクリプションを選択する

   * `az login` コマンドで Azure アカウントにサインインします。
   * `az account set -s <subscriptionguid>` コマンドを使用して、必要なサブスクリプションを選択します。

### <a name="sql-api-account"></a><a id="provision-cli-sql-api"></a>SQL API アカウント

継続的バックアップを行う SQL API アカウントをプロビジョニングするには、通常のプロビジョニング コマンドと共に追加の引数 `--backup-policy-type Continuous` を渡す必要があります。 次のコマンドは、*MyRG* リソース グループの下で "*米国西部*" リージョンに継続的バックアップ ポリシーを作成している *Pitracct* という名前の単一リージョン書き込みアカウントの例です。

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --resource-group MyRG \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

### <a name="api-for-mongodb"></a><a id="provision-cli-mongo-api"></a>MongoDB 用 API

次のコマンドは、*MyRG* リソース グループの下で "*米国西部*" リージョンに継続的バックアップ ポリシーを作成している *Pitracct* という名前の単一リージョン書き込みアカウントの例を示します。

```azurecli-interactive

az cosmosdb create \
  --name Pitracct \
  --kind MongoDB \
  --resource-group MyRG \
  --server-version "3.6" \
  --backup-policy-type Continuous \
  --default-consistency-level Session \
  --locations regionName="West US"

```

## <a name="provision-using-resource-manager-template"></a><a id="provision-arm-template"></a>Resource Manager テンプレートを使用してプロビジョニングする

Azure Resource Manager テンプレートを使用して、継続的モードで Azure Cosmos DB アカウントをデプロイできます。 アカウントをプロビジョニングするためのテンプレートを定義する場合は、次の例に示すように `backupPolicy` パラメーターを含めます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "name": "ademo-pitr1",
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "apiVersion": "2016-03-31",
      "location": "West US",
      "properties": {
        "locations": [
          {
            "locationName": "West US"
          }
        ],
        "backupPolicy": {
          "type": "Continuous"
        },
        "databaseAccountOfferType": "Standard"
      }
    }
  ]
}
```

次に、Azure PowerShell または CLI を使用してテンプレートをデプロイします。 次の例は、CLI コマンドを使用してテンプレートをデプロイする方法を示しています。

```azurecli-interactive
az group deployment create -g <ResourceGroup> --template-file <ProvisionTemplateFilePath>
```

## <a name="next-steps"></a>次のステップ

* [ライブまたは削除された Azure Cosmos DB アカウントを復元する](restore-account-continuous-backup.md)
* [定期的なバックアップから継続的バックアップにアカウントを移行する方法](migrate-continuous-backup.md)。
* [継続的バックアップ モードのリソース モデル。](continuous-backup-restore-resource-model.md)
* 継続的バックアップ モードでデータを復元するために必要な[アクセス許可を管理](continuous-backup-restore-permissions.md)します。