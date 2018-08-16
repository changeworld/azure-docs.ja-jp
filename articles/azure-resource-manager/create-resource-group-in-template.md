---
title: Azure Resource Manager テンプレートでリソース グループを作成する | Microsoft Docs
description: Azure Resource Manager テンプレートで新しいリソース グループを作成する方法について説明します。
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/07/2018
ms.author: tomfitz
ms.openlocfilehash: 90d21ac817f6fd4730ff4a7e98500a80af10ac70
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/08/2018
ms.locfileid: "39623210"
---
# <a name="create-resource-groups-in-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートでリソース グループを作成する

Azure Resource Manager テンプレートでリソース グループを作成するには、**Microsoft.Resources/resourceGroups** リソースを定義してリソース グループの名前と場所を指定します。 そのテンプレートを Azure サブスクリプションにデプロイします。 同じテンプレートでそのリソース グループにリソースをデプロイすることもできます。

この記事では、Azure CLI を使ってテンプレートをデプロイします。 現時点では、PowerShell によるサブスクリプションへのテンプレートのデプロイはサポートされていません。

## <a name="create-empty-resource-group"></a>空のリソース グループを作成する

次の例では、空のリソース グループを作成します。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Azure CLI を使ってこのテンプレートをデプロイするには、次のコマンドを使います。

```azurecli-interactive
az deployment create \
  -n demoEmptyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/emptyRG.json \
  --parameters rgName=demoRG rgLocation=northcentralus
```

## <a name="create-several-resource-groups"></a>複数のリソース グループを作成する

複数のリソース グループを作成するには、リソース グループで [copy 要素](resource-group-create-multiple.md)を使用します。 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgNamePrefix": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "instanceCount": {
            "type": "int"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[concat(parameters('rgNamePrefix'), copyIndex())]",
            "copy": {
                "name": "rgCopy",
                "count": "[parameters('instanceCount')]"
            },
            "properties": {}
        }
    ],
    "outputs": {}
}
```

Azure CLI でこのテンプレートをデプロイして 3 つのリソース グループを作成するには、次のコマンドを使います。

```azurecli-interactive
az deployment create \
  -n demoCopyRG \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/copyRG.json \
  --parameters rgNamePrefix=demoRG rgLocation=northcentralus instanceCount=3
```

## <a name="create-resource-group-and-deploy-resource"></a>リソース グループを作成してリソースをデプロイする

リソース グループを作成してそれにリソースをデプロイするには、入れ子になったテンプレートを使います。 入れ子になったテンプレートでは、リソース グループにデプロイするリソースを定義します。 リソースをデプロイする前にリソース グループを確実に存在させるには、リソース グループに依存するように入れ子になったテンプレートを設定します。

次の例では、リソース グループを作成し、ストレージ アカウントをリソース グループにデプロイします。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "rgName": {
            "type": "string"
        },
        "rgLocation": {
            "type": "string"
        },
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(subscription().id, parameters('rgName')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "location": "[parameters('rgLocation')]",
            "name": "[parameters('rgName')]",
            "properties": {}
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2017-05-10",
            "name": "storageDeployment",
            "resourceGroup": "[parameters('rgName')]",
            "dependsOn": [
                "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
            ],
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
                            "apiVersion": "2017-10-01",
                            "name": "[variables('storageName')]",
                            "location": "[parameters('rgLocation')]",
                            "kind": "StorageV2",
                            "sku": {
                                "name": "Standard_LRS"
                            }
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ],
    "outputs": {}
}
```

Azure CLI を使ってこのテンプレートをデプロイするには、次のコマンドを使います。

```azurecli-interactive
az deployment create \
  -n demoRGStorage \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/newRGWithStorage.json \
  --parameters rgName=rgStorage rgLocation=northcentralus storagePrefix=storage
```

## <a name="next-steps"></a>次の手順
* デプロイ中の依存関係のトラブルシューティングについては、「[Troubleshoot common Azure deployment errors with Azure Resource Manager (Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング)](resource-manager-common-deployment-errors.md)」を参照してください。
* Azure リソース マネージャーのテンプレートの作成の詳細については、 [テンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。 
* テンプレートで使用可能な関数の一覧については、 [テンプレートの関数](resource-group-template-functions.md)に関するページを参照してください。

