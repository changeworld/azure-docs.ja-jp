<properties 
	pageTitle="Redis キャッシュのプロビジョニング" 
	description="Azure リソース マネージャー テンプレートを使用し、Redis Cache をデプロイします。" 
	services="redis-cache" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="web" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="tomfitz"/>

# テンプレートを使用して Redis Cache を作成する

このトピックでは、Azure Redis Cache をデプロイする Azure リソース マネージャーのテンプレートを作成する方法について説明します。さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Authoring Azure Resource Manager Templates (Azure リソース マネージャー テンプレートのオーサリング)](../resource-group-authoring-templates.md)」を参照してください。

完全なテンプレートについては、「[Redis Cache のテンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json)」を参照してください。

## デプロイ対象

このテンプレートでは、Azure Redis Cache をデプロイします。

展開を自動的に実行するには、次のボタンをクリックします。

[![Azure への展開](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む Parameters という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレートにおいて、それぞれのパラメーターの値は、デプロイ対象のリソースを定義するために使用されます。

テンプレートに含まれるそれぞれのパラメーターについて説明します。

[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### redisCacheLocation

Redis Cache の場所。最適なパフォーマンスのために、アプリケーションとキャッシュで同じ場所を使用することをお勧めします。

    "redisCacheLocation": {
      "type": "string"
    }

    
## デプロイ対象のリソース

### Redis Cache

Azure Redis Cache を作成します。

    {
      "apiVersion": "2014-04-01-preview",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "sku": {
          "name": "[parameters('redisCacheSKU')]",
          "family": "[parameters('redisCacheFamily')]",
          "capacity": "[parameters('redisCacheCapacity')]"
        },
        "redisVersion": "[parameters('redisCacheVersion')]",
        "enableNonSslPort": true
      }
    }
     



## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

    New-AzureResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### Azure CLI

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup

<!---HONumber=July15_HO4-->