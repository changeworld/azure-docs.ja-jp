<properties
    pageTitle="Azure Resource Manager テンプレートを使用したトピックとサブスクリプションを含んだ Service Bus 名前空間の作成 | Microsoft Azure"
    description="Azure Resource Manager テンプレートを使用したトピックとサブスクリプションを含んだ Service Bus 名前空間の作成"
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
    ms.date="07/11/2016"
    ms.author="sethm;shvija"/>

# Azure Resource Manager テンプレートを使用したトピックとサブスクリプションを含んだ Service Bus 名前空間の作成

この記事では、Azure Resource Manager テンプレートを使用して、トピックとサブスクリプションを含んだ Service Bus の名前空間を作成する方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、[Service Bus の名前空間にトピックとサブスクリプションを追加する][]テンプレートを参照してください。

>[AZURE.NOTE] 次の Azure Resource Manager テンプレートを、ダウンロードしてデプロイすることができます。
>
>-    [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-event-hub.md)
>-    [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
>-    [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
>
>最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」ギャラリーで "Service Bus" を検索してください。

## デプロイの対象

このテンプレートでデプロイされるのは、トピックとサブスクリプションを含んだ Service Bus 名前空間です。

[Service Bus のトピックとサブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)は、*発行とサブスクライブ*のパターンで一対多の形式の通信を実現します。

展開を自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-and-subscription%2Fazuredeploy.json)

## パラメーター

Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。常に同じ値に対してはパラメーターを定義しないでください。テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### serviceBusNamespaceName

作成する Service Bus 名前空間の名前。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### serviceBusTopicName

Service Bus 名前空間に作成するトピックの名前。

```
"serviceBusTopicName": {
"type": "string"
}
```

### serviceBusSubscriptionName

Service Bus 名前空間に作成するサブスクリプションの名前。

```
"serviceBusSubscriptionName": {
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

**Messaging** タイプの標準的な Service Bus 名前空間を作成し、トピックとサブスクリプションを追加します。

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]",
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {}
            }]
        }]
    }]
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureResourceGroupDeployment -Name <deployment-name> -ResourceGroupName <resource-group-name> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-and-subscription/azuredeploy.json>
```

## 次のステップ

Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
- [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
- [Service Bus リソースを Service Bus Explorer で管理する](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Service Bus の名前空間にトピックとサブスクリプションを追加する]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-and-subscription/

<!---HONumber=AcomDC_0713_2016-->