<properties
    pageTitle="Service Bus に使用する承認規則を Azure Resource Manager テンプレートで作成する | Microsoft Azure"
    description="Service Bus の名前空間とキューに使用する承認規則を Azure Resource Manager テンプレートで作成する"
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

# Service Bus の名前空間とキューに使用する承認規則を Azure Resource Manager テンプレートで作成する

この記事では、Azure Resource Manager テンプレートを使用して、Service Bus の名前空間とキューに使用する承認規則を作成する方法について説明します。デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、「[Azure Resource Manager のテンプレートの作成][]」を参照してください。

完全なテンプレートについては、GitHub の [Service Bus 承認規則テンプレート][]を参照してください。

>[AZURE.NOTE] 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
>
>-    [Event Hub とコンシューマー グループを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-event-hub.md)
>-    [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
>-    [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
>-    [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
>
>最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][]」で「Service Bus」を検索してください。

## デプロイの対象

このテンプレートでデプロイされるのは、メッセージング タイプの Service Bus 名前空間のエンティティ (このケースではキュー) に使用する承認規則です。

このテンプレートでは、Shared Access Signature (SAS) を認証に使用します。SAS 認証により、アプリケーションは、名前空間、または特定の権限が関連付けられているメッセージ エンティティ (キューまたはトピック) で構成されたアクセス キーを使用して Service Bus に対して認証できます。次に、このキーを使用して、クライアントが後で Service Bus に対する認証に使用できる SAS トークンを生成できます。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

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

### namespaceAuthorizationRuleName 

名前空間の承認規則の名前。

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### serviceBusQueueName

Service Bus 名前空間のキューの名前。

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

**Messaging** タイプの標準的な Service Bus 名前空間を作成すると共に、Service Bus の名前空間とエンティティに使用する承認規則を作成します。

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## デプロイを実行するコマンド

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## Azure CLI

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## 次のステップ

Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

- [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
- [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
- [Service Bus リソースを Service Bus Explorer で管理する](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Service Bus の認証と承認](service-bus-authentication-and-authorization.md)

  [Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
  [Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Service Bus 承認規則テンプレート]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/

<!---HONumber=AcomDC_0518_2016-->