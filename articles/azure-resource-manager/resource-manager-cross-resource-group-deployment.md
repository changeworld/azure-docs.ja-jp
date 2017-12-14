---
title: "複数のサブスクリプションとリソース グループに Azure リソースをデプロイする | Microsoft Docs"
description: "デプロイ時に複数の Azure サブスクリプションとリソース グループを対象にする方法について説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>複数のサブスクリプションまたはリソース グループに Azure リソースをデプロイする

テンプレートに含まれているリソースはすべて 1 つのリソース グループにデプロイするのが一般的です。 一方、さまざまなリソースを 1 つにまとめたうえで、複数のリソース グループまたはサブスクリプションにデプロイしたい状況もあります。 たとえば Azure Site Recovery に使うバックアップ仮想マシンは、別のリソース グループと場所にデプロイした方がよい場合があります。 Resource Manager では、入れ子になったテンプレートを使用することで、親テンプレートに使われているサブスクリプションおよびリソース グループとは異なる、サブスクリプションとリソース グループを対象にすることができます。

アプリケーションとその一連のリソースにとって、リソース グループはライフサイクルのコンテナーと言えます。 リソース グループはテンプレートの外で作成し、デプロイ時にその対象として、そのリソース グループを指定することになります。 リソース グループの概要については、「[Azure Resource Manager の概要](resource-group-overview.md)」を参照してください。

## <a name="specify-a-subscription-and-resource-group"></a>サブスクリプションとリソース グループの指定

異なるリソースをデプロイ対象とするには、入れ子になった (リンクされた) テンプレートをデプロイ時に使う必要があります。 `Microsoft.Resources/deployments` リソース タイプは、`subscriptionId` および `resourceGroup` にパラメーターを提供します。 こうしたプロパティを使用すると、入れ子になったデプロイ用に異なるサブスクリプションとリソース グループを指定することができます。 すべてのリソース グループは、デプロイの実行前に存在している必要があります。 サブスクリプション ID またはリソース グループを指定しない場合は、親テンプレートのサブスクリプションおよびリソース グループが使用されます。

次の例では、2 つのストレージ アカウントをデプロイしています。1 つは、デプロイ時に指定されるリソース グループのストレージ アカウントで、もう 1 つは、`secondResourceGroup` パラメーターで指定されるリソース グループのストレージ アカウントです。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

存在しないリソース グループの名前を `resourceGroup` に設定した場合、デプロイは失敗します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

テンプレートの例をデプロイするには、2017 年 5 月以降のリリースの Azure PowerShell または Azure CLI を使用します。 この例では、GitHub の[クロス サブスクリプション テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json)を使用してください。

### <a name="two-resource-groups-in-the-same-subscription"></a>同じサブスクリプションの 2 つのリソース グループ

PowerShell の場合、2 つのストレージ アカウントを、同じサブスクリプションの 2 つのリソース グループにデプロイするには、次を使用します。

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Azure CLI の場合、2 つのストレージ アカウントを、同じサブスクリプションの 2 つのリソース グループにデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

デプロイが完了すると、2 つのリソース グループが確認できます。 それぞれのリソース グループにストレージ アカウントが存在します。

### <a name="two-resource-groups-in-different-subscriptions"></a>異なるサブスクリプションの 2 つのリソース グループ

PowerShell の場合、2 つのストレージ アカウントを、2 つのサブスクリプションにデプロイするには、次を使用します。

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

Azure CLI の場合、2 つのストレージ アカウントを、2 つのサブスクリプションにデプロイするには、次を使用します。

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

## <a name="use-the-resourcegroup-function"></a>resourceGroup() 関数の使用

リソース グループ間のデプロイの場合、[resourceGroup() 関数](resource-group-template-functions-resource.md#resourcegroup)は、入れ子になったテンプレートの指定方法に基づいてさまざまに解決されます。 

あるテンプレートを別のテンプレートに埋め込む場合、入れ子になったテンプレート内の resourceGroup() は親リソース グループに解決されます。 埋め込みテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

別のテンプレートにリンクする場合、リンクされたテンプレート内の resourceGroup() は入れ子になったリソース グループに解決されます。 リンクされたテンプレートでは次の形式が使用されます。

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

`resourceGroup()` によるさまざまな解決方法をテストするには、親テンプレート、インライン テンプレート、およびリンクされたテンプレートのリソース グループ オブジェクトを返す[サンプル テンプレート](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json)をデプロイします。 親テンプレートとインライン テンプレートの両方が、同じリソース グループに解決されます。 リンクされたテンプレートは、リンクされたリソース グループに解決されます。

PowerShell では、次を使用します。

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Azure CLI では、次を使用します。

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>次のステップ

* テンプレートでパラメーターを定義する方法については、「[Azure Resource Manager テンプレートの構造と構文の詳細](resource-group-authoring-templates.md)」を参照してください。
* 一般的なデプロイ エラーを解決するうえでのヒントについては、「[Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング](resource-manager-common-deployment-errors.md)」を参照してください。
* SAS トークンを必要とするテンプレートをデプロイする方法については、「[Deploy private template with SAS token (SAS トークンを使用したプライベート テンプレートのデプロイ)](resource-manager-powershell-sas-token.md)」を参照してください。
