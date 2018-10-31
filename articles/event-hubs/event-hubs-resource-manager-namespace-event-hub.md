---
title: テンプレートを使用した Azure Event Hubs 名前空間とコンシューマー グループの作成 | Microsoft Docs
description: イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 10/16/2018
ms.author: shvija
ms.openlocfilehash: 48d41ef4df986f959dfabe04e07552e287fdfcd0
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457092"
---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する
Azure Event Hubs はビッグ データ ストリーミング プラットフォームであり、毎秒数百万のイベントを受け取って処理できるイベント インジェスト サービスです。 Event Hubs では、分散されたソフトウェアやデバイスから生成されるイベント、データ、またはテレメトリを処理および格納できます。 イベント ハブに送信されたデータは、任意のリアルタイム分析プロバイダーやバッチ処理/ストレージ アダプターを使用して、変換および保存できます。 Event Hubs の詳しい概要については、[Event Hubs の概要](event-hubs-about.md)と [Event Hubs の機能](event-hubs-features.md)に関するページをご覧ください。

このクイック スタートでは、Azure Resource Manager テンプレートを使ってイベント ハブを作成します。 Azure Resource Manager テンプレートを使用して、イベント ハブとコンシューマー グループをそれぞれ 1 つずつ含んだ [Event Hubs](event-hubs-what-is-event-hubs.md) タイプの名前空間を作成します。 記事では、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法を説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成の詳細については、[Azure Resource Manager テンプレートのオーサリング][Authoring Azure Resource Manager templates]に関する記事をご覧ください。

完全なテンプレートについては、GitHub の[イベント ハブとコンシューマー グループを作成するためのテンプレート][Event Hub and consumer group template]を参照してください。

> [!NOTE]
> 最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート][Azure Quickstart Templates]」ギャラリーで "Event Hubs" を検索してください。

## <a name="prerequisites"></a>前提条件
このクイック スタートを完了するには、Azure サブスクリプションが必要です。 お持ちでない場合は、開始する前に無料アカウントを作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

**Azure PowerShell** を使用してローカルに Resource Manager テンプレートをデプロイする場合、PowerShell の最新バージョンを実行してこのクイック スタートを完了する必要があります。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell モジュールのインストールと構成](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)に関するページを参照してください。

**Azure CLI** をインストールして使用してローカルに Resource Manager テンプレートをデプロイする場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには `az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="what-will-you-deploy"></a>デプロイの対象

このテンプレートでデプロイされるのは、イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間です。

デプロイを自動的に実行するには、次のボタンを選択します。

[![Azure へのデプロイ](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="define-parameters"></a>パラメーターを定義する
Azure リソース マネージャーを使用して、テンプレートのデプロイ時に値を指定するパラメーターを定義します。 テンプレートには、すべてのパラメーター値を含む `Parameters` という名前のセクションがあります。 デプロイするプロジェクトやデプロイ先の環境に基づいて変化する値に対して、パラメーターを定義する必要があります。 常に同じ値に対してはパラメーターを定義しないでください。 テンプレート内のそれぞれのパラメーターの値は、デプロイされるリソースを定義します。

このテンプレートでは、次のパラメーターを定義します。

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

作成する Event Hubs 名前空間の名前。

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

Event Hubs 名前空間に作成するイベント ハブの名前。

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

イベント ハブに対して作成するコンシューマー グループの名前。

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

テンプレートの API バージョン。

```json
"apiVersion": {
"type": "string"
}
```

## <a name="define-resources-to-deploy"></a>デプロイするリソースの定義

イベント ハブとコンシューマー グループを含んだ、**EventHubs** タイプの名前空間を作成します。

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
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

## <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell を使用してリソースをデプロイするには、次のコマンドを実行します。

```azurepowershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Azure CLI
Azure PowerShell を使用してリソースをデプロイするには、次のコマンドを実行します。

```azurecli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

お疲れさまでした。 Azure Resource Manager テンプレートを使用して Event Hubs 名前空間を作成し、その名前空間内にイベント ハブを作成しました。

## <a name="next-steps"></a>次の手順

この記事では、Event Hubs 名前空間を作成し、サンプル アプリケーションを使用してイベント ハブからイベントを送受信しました。 イベント ハブに対してイベントを送信または受信するためのステップ バイ ステップの手順については、次のチュートリアルをご覧ください。 

- **イベントをイベント ハブに送信する**: [.NET Standard](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **イベントからイベント ハブを受信する**: [.NET Standard](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
