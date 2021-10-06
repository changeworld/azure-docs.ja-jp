---
title: Azure Cache for Redis で Web アプリをプロビジョニングする
description: Azure Resource Manager テンプレートを使用し、Web アプリと Azure Cache for Redis を展開します。
services: app-service
author: curib
ms.service: app-service
ms.topic: conceptual
ms.date: 01/06/2017
ms.author: cauribeg
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 71648781edd8d202add7ce2a7cc6584173f0451b
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537459"
---
# <a name="create-a-web-app-plus-azure-cache-for-redis-using-a-template"></a>テンプレートを使用して Web アプリと Azure Cache for Redis を作成する

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

この記事では、Azure Web アプリと Azure Cache for Redis をデプロイする Azure Resource Manager のテンプレートを作成する方法について説明します。 次のデプロイの詳細について説明します。

- デプロイするリソースを定義する方法 
- デプロイの実行時に指定されるパラメーターの定義方法

このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「 [Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../azure-resource-manager/templates/syntax.md)」を参照してください。 キャッシュ リソースの種類の JSON 構文とプロパティについては、「[Microsoft.Cache resource types (Microsoft.Cache リソースの種類)](/azure/templates/microsoft.cache/allversions)」を参照してください。

完全なテンプレートについては、「[Web アプリと Azure Cache for Redis のテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json)」を参照してください。

## <a name="what-you-will-deploy"></a>デプロイ対象
このテンプレートでは、以下をデプロイします。

* Azure Web アプリ
* Azure Cache for Redis

デプロイを自動的に実行するには、次のボタンを選択します。

[![Azure へのデプロイ](./media/cache-web-app-arm-with-redis-cache-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.web%2Fweb-app-with-redis-cache%2Fazuredeploy.json)

## <a name="parameters-to-specify"></a>指定するパラメーター
[!INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

[!INCLUDE [cache-deploy-parameters](../../includes/cache-deploy-parameters.md)]

## <a name="variables-for-names"></a>名前用の変数
このテンプレートでは、リソースの名前を作成する変数を使用します。 [uniqueString](../azure-resource-manager/templates/template-functions-string.md#uniquestring) 関数を使用し、リソース グループの ID に基づく値を構築します。

```json
"variables": {
  "hostingPlanName": "[concat('hostingplan', uniqueString(resourceGroup().id))]",
  "webSiteName": "[concat('webSite', uniqueString(resourceGroup().id))]",
  "cacheName": "[concat('cache', uniqueString(resourceGroup().id))]"
},
```


## <a name="resources-to-deploy"></a>デプロイ対象のリソース
[!INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="azure-cache-for-redis"></a>Azure Cache for Redis
Web アプリで使用される Azure Cache for Redis を作成します。 キャッシュの名前は **cacheName** 変数で指定されます。

テンプレートによって、リソース グループと同じ場所にキャッシュが作成されます。

```json
{
  "name": "[variables('cacheName')]",
  "type": "Microsoft.Cache/Redis",
  "location": "[resourceGroup().location]",
  "apiVersion": "2015-08-01",
  "dependsOn": [ ],
  "tags": {
    "displayName": "cache"
  },
  "properties": {
    "sku": {
      "name": "[parameters('cacheSKUName')]",
      "family": "[parameters('cacheSKUFamily')]",
      "capacity": "[parameters('cacheSKUCapacity')]"
    }
  }
}
```


### <a name="web-app-azure-cache-for-redis"></a>Web アプリ (Azure Cache for Redis)
**webSiteName** 変数で指定された名前で Web アプリを作成します。

Azure Cache for Redis との連動を可能にするアプリ設定プロパティで Web アプリが構成されることに注意してください。 これらのアプリ設定はデプロイ時に指定された値に基づいて動的に作成されます。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/Redis/', variables('cacheName'))]"
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),'.redis.cache.windows.net,abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/Redis', variables('cacheName')), '2015-08-01').primaryKey)]"
      }
    }
  ]
}
```


### <a name="web-app-redisenterprise"></a>Web アプリ (RedisEnterprise)
RedisEnterprise の場合、リソースの種類は少し異なるので、**listKeys** を行なう方法は違います。

```json
{
  "apiVersion": "2015-08-01",
  "name": "[variables('webSiteName')]",
  "type": "Microsoft.Web/sites",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Web/serverFarms/', variables('hostingPlanName'))]"
  ],
  "tags": {
    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', variables('hostingPlanName'))]": "empty",
    "displayName": "Website"
  },
  "properties": {
    "name": "[variables('webSiteName')]",
    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
  },
  "resources": [
    {
      "apiVersion": "2015-08-01",
      "type": "config",
      "name": "appsettings",
      "dependsOn": [
        "[concat('Microsoft.Web/Sites/', variables('webSiteName'))]",
        "[concat('Microsoft.Cache/RedisEnterprise/databases/', variables('cacheName'), "/default")]",
      ],
      "properties": {
       "CacheConnection": "[concat(variables('cacheHostName'),abortConnect=false,ssl=true,password=', listKeys(resourceId('Microsoft.Cache/RedisEnterprise', variables('cacheName'), 'default'), '2020-03-01').primaryKey)]"
      }
    }
  ]
}
```

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```azurepowershell
New-AzResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/quickstarts/microsoft.web/web-app-with-redis-cache/azuredeploy.json -g ExampleDeployGroup
```