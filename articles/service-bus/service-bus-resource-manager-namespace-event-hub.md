<properties
    pageTitle="Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する | Microsoft Azure"
    description="Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="04/15/2016"
    ms.author="sethm;shvija"/>

# Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する

この記事では、Azure Resource Manager テンプレートを使用し、イベント ハブとコンシューマー グループを含んだ Service Bus 名前空間を作成する方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、GitHub にアクセスし、[Service Bus のイベント ハブとコンシューマー グループを作成するためのテンプレート][]を参照してください。

>[AZURE.NOTE] 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
>
>-    [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
>-    [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
>-    [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
>-    [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
>
>最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」で「Service Bus」を検索してください。

## デプロイの対象

このテンプレートでデプロイされるのは、イベント ハブとコンシューマー グループを含んだ Service Bus 名前空間です。

Event Hubs は、Azure への大規模なイベントとテレメトリ受信をわずかな遅延と高い信頼性で提供するために使用される、イベント処理サービスです。

詳細については、[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) に関する情報を参照してください。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-eventhub-and-consumergroup%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

テンプレートに含まれるそれぞれのパラメーターについて説明します。

### serviceBusNamespaceName

作成する Service Bus 名前空間の名前。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusEventHubName

Service Bus 名前空間に作成するイベント ハブの名前。

```
"serviceBusEventHubName": {
"type": "string"
}
```

### serviceBusConsumerGroupName

Service Bus 名前空間のイベント ハブに対して作成するコンシューマー グループの名前。

```
"serviceBusConsumerGroupName": {
"type": "string"
}
```

### serviceBusApiVersion

テンプレートの Service Bus API バージョン。

```
"serviceBusApiVersion": {
"type": "string"
}
```

## デプロイ対象のリソース

イベント ハブとコンシューマー グループを含んだ、**イベント ハブ** タイプの Service Bus 名前空間を作成します。

```
"resources": [
        {
            "apiVersion": "[variables('ehVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/Namespaces",
            "location": "[variables('location')]",
            "kind": "EventHub",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('ehVersion')]",
                    "name": "[parameters('serviceBusEventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusEventHubName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('ehVersion')]",
                            "name": "[parameters('serviceBusConsumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('serviceBusEventHubName')]"
                            ],
                            "properties": {
                            }
                        }
                    ]
                }
            ]
        }
    ]
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-eventhub-and-consumergroup/azuredeploy.json][]
```

## 次のステップ

Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
- [PowerShell を使用した Event Hubs の管理](service-bus-powershell-how-to-provision.md)
- [Service Bus Explorer を使用した Event Hubs リソースの管理](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Service Bus のイベント ハブとコンシューマー グループを作成するためのテンプレート]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-eventhub-and-consumergroup/

<!---HONumber=AcomDC_0518_2016-->