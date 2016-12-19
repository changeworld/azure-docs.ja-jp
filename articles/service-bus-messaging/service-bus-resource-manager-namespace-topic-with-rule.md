---
title: "トピック、サブスクリプション、ルールを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する | Microsoft Docs"
description: "トピック、サブスクリプション、ルールを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成する"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 9e0aaf58-0214-4bca-bd00-d29c08f9b1bc
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/25/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a1a5d9d6fa62bee7b2b463ddc89fe6c99740e03d


---
# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>トピック、サブスクリプション、ルールを含んだ Service Bus 名前空間を Azure Resource Manager テンプレートで作成します。
この記事では、Azure Resource Manager テンプレートを使用して、トピック、サブスクリプション、ルール (フィルター) を含んだ Service Bus の名前空間を作成する方法について説明します。 デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法について説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。

テンプレートの作成の詳細については、[「Azure Resource Manager テンプレートの作成」][Azure Resource Manager のテンプレートの作成]を参照してください。

Azure リソースの名前付け規則の慣習とパターンの詳細については、[Azure リソースの名前付け規則][Azure リソースの名前付け規則]に関するページを参照してください。

完全なテンプレートについては、[Service Bus の名前空間にトピック、サブスクリプション、ルールを追加する][Service Bus の名前空間にトピック、サブスクリプション、ルールを追加する]テンプレートを参照してください。

> [!NOTE]
> 次の Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。
> 
> * [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
> * [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
> * [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
> * [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
> 
> 最新のテンプレートを確認する場合は、[「Azure クイックスタート テンプレート」][Azure クイックスタート テンプレート]ギャラリーで "Service Bus" を検索してください。
> 
> 

## <a name="what-will-you-deploy"></a>デプロイの対象
このテンプレートでデプロイされるのは、トピック、サブスクリプション、ルール (フィルター) を含んだ Service Bus 名前空間です。

[Service Bus のトピックとサブスクリプション](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions)は、"*発行とサブスクライブ*" のパターンで一対多の形式の通信を実現します。 トピックとサブスクリプションを使用しているとき、分散アプリケーションのコンポーネントは互いに直接通信するのではなく、仲介者として機能するトピックを介してメッセージをやり取りします。トピックのサブスクリプションとは、トピックに送信されたメッセージのコピーを受け取るキューと実質的には考えることができます。 サブスクリプションにフィルターを設定すると、トピックに送信されたメッセージのうち、その特定のサブスクリプションで取得できるメッセージを指定することができます。

## <a name="what-are-rules-filters"></a>ルール (フィルター) とは
多くのシナリオでは、特性のあるメッセージは、異なる方法で処理する必要があります。 これを実現するために、目的のプロパティを持つメッセージを検索し、該当するプロパティに特定の変更を行うようにサブスクリプションを構成できます。 Service Bus のサブスクリプションには、トピックに送信されたすべてのメッセージが表示されますが、仮想サブスクリプション キューにコピーできるのは、これらのメッセージの一部のみです。 これを行うには、サブスクリプション フィルターを使用します。 ルール (フィルター) の詳細については、[「Service Bus のキュー、トピック、サブスクリプション」][Service Bus のキュー、トピック、サブスクリプション]を参照してください。

デプロイメントを自動的に実行するには、次のボタンをクリックします。

[![Azure へのデプロイ](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>パラメーター
テンプレートのデプロイ時に指定したい値については、Azure Resource Manager パラメーターを定義する必要があります。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 これらの値のパラメーターを定義する必要があります。これらの値は、デプロイするプロジェクトやデプロイ先の環境に応じて異なります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義するために使用されます。

このテンプレートでは、次のパラメーターを定義します。

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName
作成する Service Bus 名前空間の名前。

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName
Service Bus 名前空間に作成するトピックの名前。

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName
Service Bus 名前空間に作成するサブスクリプションの名前。

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName
Service Bus 名前空間に作成するルール (フィルター) の名前。

```
   "serviceBusRuleName": {
   "type": "string",
  }
```
### <a name="servicebusapiversion"></a>serviceBusApiVersion
テンプレートの Service Bus API バージョン。

```
"serviceBusApiVersion": {
"type": "string"
}
```
## <a name="resources-to-deploy"></a>デプロイ対象のリソース
**Messaging** タイプの標準的な Service Bus 名前空間を作成し、トピックとサブスクリプションとルールを追加します。

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
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
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>デプロイを実行するコマンド
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Azure CLI
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>次のステップ
Azure Resource Manager を使ってリソースを作成、デプロイしたら、それらのリソースを管理する方法を次の記事で確認しましょう。

* [Azure Automation を使用した Azure Service Bus の管理](service-bus-automation-manage.md)
* [PowerShell で Service Bus を管理する](service-bus-powershell-how-to-provision.md)
* [Service Bus リソースを Service Bus Explorer で管理する](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

[Azure Resource Manager のテンプレートの作成]: ../resource-group-authoring-templates.md
[Azure クイックスタート テンプレート]: https://azure.microsoft.com/documentation/templates/?term=service+bus
[Service Bus のトピックとサブスクリプションの詳細については、こちらを参照してください。]: service-bus-queues-topics-subscriptions.md
[Azure リソース マネージャーでの Azure PowerShell の使用]: ../powershell-azure-resource-manager.md
[Azure リソース管理での、Mac、Linux、および Windows 用 Azure CLI の使用]: ../xplat-cli-azure-resource-manager.md
[Azure リソースの名前付け規則]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Service Bus の名前空間にトピック、サブスクリプション、ルールを追加する]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
[Service Bus のキュー、トピック、サブスクリプション]:service-bus-queues-topics-subscriptions.md




<!--HONumber=Nov16_HO3-->


