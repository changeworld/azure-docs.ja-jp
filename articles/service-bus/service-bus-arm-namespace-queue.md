<properties
    pageTitle="キューを含んだ Service Bus 名前空間を作成する | Microsoft Azure"
    description="Service Bus の名前空間とキューを ARM テンプレートで作成する"
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

# Service Bus の名前空間とキューを ARM テンプレートで作成する

この記事では、Azure Resource Manager (ARM) テンプレートを使用して、Service Bus の名前空間とキューを作成する方法について説明します。さらに、デプロイメント対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、GitHub にある [Service Bus の名前空間とキューのテンプレート][]を参照してください。

>[AZURE.NOTE] 次の ARM テンプレートをダウンロードしてデプロイすることができます。
>
>-    [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-auth-rule.md)
>-    [Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-event-hub.md)
>-    [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-arm-namespace-topic.md)
>-    [Service Bus 名前空間の作成](service-bus-arm-namespace.md)
>
>最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」で「Service Bus」を検索してください。

## デプロイの対象

このテンプレートでは、キューを含んだ Service Bus 名前空間をデプロイします。

キューでは、コンシューマーが競合している場合のメッセージ配信に先入先出法 (FIFO) を使用します。

[Service Bus のキューの詳細については、こちらを参照してください。](service-bus-queues-topics-subscriptions.md)

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-arm-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

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

### serviceBusQueueName

Service Bus 名前空間に作成するキューの名前。

```
"serviceBusQueueName": {
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

**Messaging** タイプの標準的な Service Bus 名前空間を作成し、キューを追加します。

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
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## 次のステップ

ARM を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
- [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
- [Service Bus リソースを Service Bus Explorer で管理する](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Service Bus の名前空間とキューのテンプレート]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md

<!---HONumber=AcomDC_0420_2016-->