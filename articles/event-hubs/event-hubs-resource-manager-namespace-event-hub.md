<properties
    pageTitle="Event Hubs とコンシューマー グループを含んだイベント ハブ名前空間を Azure Resource Manager テンプレートで作成する | Microsoft Azure"
    description="Event Hubs とコンシューマー グループを含んだイベント ハブ名前空間を Azure Resource Manager テンプレートで作成する"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する

この記事では、Azure Resource Manager テンプレートを使用し、イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を作成する方法について説明します。さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、GitHub にアクセスし、[イベント ハブとコンシューマー グループを作成するためのテンプレート][]をご覧ください。

>[AZURE.NOTE]
最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」ギャラリーで "Event Hubs" を検索してください。

## デプロイの対象

このテンプレートでデプロイされるのは、イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間です。

[Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) は、Azure への大規模なイベントとテレメトリ受信をわずかな待機時間と高い信頼性で提供するために使用される、イベント処理サービスです。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### eventHubNamespaceName

作成する Event Hubs 名前空間の名前。

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

Event Hubs 名前空間に作成するイベント ハブの名前。

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

イベント ハブに対して作成するコンシューマー グループの名前。

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### apiVersion

テンプレートの API バージョン。

```
"apiVersion": {
"type": "string"
}
```

## デプロイ対象のリソース

イベント ハブとコンシューマー グループを含んだ、**EventHubs** タイプの名前空間を作成します。

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## 次のステップ

Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Service Bus Explorer を使用した Event Hubs リソースの管理](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [イベント ハブとコンシューマー グループを作成するためのテンプレート]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

<!---HONumber=AcomDC_0907_2016-->