---
title: Azure Cosmos DB アカウントのマルチマスターを有効にする
description: この記事では、Azure portal、PowerShell、CLI、または Azure Resource Manager テンプレートを使用して Azure Cosmos DB アカウントを作成するときに、マルチマスターのサポートを有効にする方法について説明します。
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: 71579e28f389d91498ef5f37c5d43dc9d5140234
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963221"
---
# <a name="enable-multi-master-for-azure-cosmos-db-accounts"></a>Azure Cosmos DB アカウントのマルチマスターを有効にする

マルチマスターのサポートは、Azure Cosmos DB アカウントの作成中に有効になります。 Azure Cosmos DB アカウントは、Azure portal、PowerShell、CLI、または Azure Resource Manager テンプレートを使用して作成することができます。

> [!IMPORTANT]
> 現時点では、新しい Azure Cosmos DB アカウントに対してのみ、マルチマスターのサポートを有効にすることができます。 既存の Azure Cosmos DB アカウントでは、この機能を使用できません。 既存のアカウントもサポートできるよう作業を進めておりますので、利用可能になったらお知らせします。

Azure Cosmos DB アカウントをマルチマスターのサポート付きで作成した後は、データベースやコンテナーの作成、ドキュメントのアップロード、競合解決ポリシーの割り当てを行うことができます。 マルチマスターでの競合解決とコード サンプルについては、[マルチマスターのコード サンプル](multi-master-conflict-resolution.md#code-samples)に関する記事を参照してください。

## <a name="enable-multi-master-using-azure-portal"></a>Azure portal を使用してマルチマスターを有効にする

1. [Azure ポータル](https://portal.azure.com/)

2. **[リソースの作成]、[データベース]、[Azure Cosmos DB]** の順に選択します。

3. **[新しいアカウント]** ウィンドウで、新しい Azure Cosmos DB アカウントの以下の設定を入力します。

   |**設定**  |**推奨値** |**説明**|
   |---------|---------|---------|
   |サブスクリプション   | {自分のサブスクリプション}  |この Azure Cosmos DB アカウントに使用する Azure サブスクリプションを選択します。  |
   |リソース グループ  |   {自分のリソース グループの名前}    |  既存のリソース グループを選択するか、 **[新規作成]** を選択してアカウントの新しいリソース グループ名を入力します。 |
   |アカウント名 | {自分のアカウント名}   |  自分の Azure Cosmos DB アカウントを識別するための一意の名前を入力します。        |
   |API  |   任意   |  API の種類を選択します。   |
   |Location  | 任意のリージョンを選択します   | Azure Cosmos DB アカウントをホストする地理的な場所を入力します。 このアカウントは複数のリージョンで使用されるため、任意のリージョンを選択することができます。  |
   |Geo 冗長の有効化   |  有効化  |  マルチマスターを以下で選択できるように、有効化を選択します。   |
   |マルチ マスターを有効にする | 有効化  | このアカウントのマルチマスターの有効化を選択します。 |


## <a name="using-multi-master-in-sdks"></a>SDK でマルチマスターを使用する

アプリケーション内でマルチマスターが有効になっているアカウントを使用すると、以下のように ConnectionPolicy を活用してマルチマスター機能を利用できます。

```csharp
ConnectionPolicy policy = new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct,
   ConnectionProtocol = Protocol.Tcp,
   UseMultipleWriteLocations = true,
};
policy.PreferredLocations.Add("West US");
policy.PreferredLocations.Add("North Europe");
policy.PreferredLocations.Add("Southeast Asia");
```

## <a name="enable-multi-master-using-powershell"></a>PowerShell を使用してマルチマスターを有効にする

`enableMultipleWriteLocations` パラメーターを "true" に設定することによって、マルチマスターが有効になっている Cosmos DB アカウントを作成することもできます。 マルチマスターが有効になっている Azure Cosmos DB アカウントを作成するには、PowerShell ウィンドウを開き、次のスクリプトを実行します。

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = "<ip-range-filter>"

$consistencyPolicy = @{"defaultConsistencyLevel"="Session";
                       "maxIntervalInSeconds"= "10";
                       "maxStalenessPrefix"="200"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

## <a name="enable-multi-master-using-cli"></a>CLI を使用してマルチマスターを有効にする

enable-multiple-write-locations パラメーターを "true" に設定することによって、マルチマスターを有効にすることができます。 マルチマスターが有効になっている Azure Cosmos DB アカウントを作成するには、Azure CLI またはクラウド シェルを開き、次のコマンドを実行します。

```azurecli-interactive
az cosmosdb create \
   –-name "myCosmosDbAccount" \
   --resource-group "myResourceGroup" \
   --default-consistency-level "Session" \
   --enable-automatic-failover "true" \
   --locations "EastUS=0" "WestUS=1" \
   --enable-multiple-write-locations true \
```

## <a name="enable-multi-master-using-resource-manager-template"></a>Resource Manager テンプレートを使用してマルチマスターを有効にする

次の JSON コードは、Azure Cosmos DB アカウントのデプロイに使用できる Resource Manager テンプレートの例です。 Resource Manager テンプレートの形式と構文について学習するには、[Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) のドキュメントを参照してください。 このテンプレートで注意する必要がある主要なパラメーターは "enableMultipleWriteLocations" で、その値は true です。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String"
        },
        "locationName": {
            "type": "String"
        },
        "defaultExperience": {
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2015-04-08",
            "location": "[parameters('location')]",
            "tags": {
                "defaultExperience": "[parameters('defaultExperience')]"
            },
            "properties": {
                "databaseAccountOfferType": "Standard",
                "locations": [
                    {
                        "id": "[concat(parameters('name'), '-', parameters('location'))]",
                        "failoverPriority": 0,
                        "locationName": "[parameters('locationName')]"
                    }
                ],
                "isVirtualNetworkFilterEnabled": false,
                "enableMultipleWriteLocations": true,
                "virtualNetworkRules": [],
                "dependsOn": []
            }
        }
    ]
}
```

## <a name="next-steps"></a>次の手順

この記事では、Azure Cosmos DB アカウントでマルチマスターのサポートを有効にする方法について説明しました。 次に、以下のリソースをご覧ください。

* [オープン ソース NoSQL データベースでのマルチマスターの使用](multi-master-oss-nosql.md)

* [Azure Cosmos DB での競合の解決について](multi-master-conflict-resolution.md)
