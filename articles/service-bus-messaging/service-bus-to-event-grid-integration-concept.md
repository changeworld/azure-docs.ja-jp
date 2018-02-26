---
title: "Azure Service Bus と Event Grid の統合の概要 | Microsoft Docs"
description: "Service Bus メッセージングと Event Grid の統合の説明"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: 17f6c107ea1adfd4463ff4cc205fe11d111acb84
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/21/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Azure Service Bus と Azure Event Grid の統合の概要

Azure Service Bus では、Azure Event Grid との新しい統合が利用可能になりました。 この機能によって可能になる重要なシナリオは、メッセージが少量の Service Bus キューまたはサブスクリプションで、メッセージのレシーバー ポーリングを常に行う必要がなくなるというものです。 Service Bus では、レシーバーがない場合にキューまたはサブスクリプションにメッセージがあるとき、Azure Event Grid にイベントを発行できるようになりました。 Azure Event Grid サブスクリプションを Service Bus 名前空間に作成してこれらのイベントをリッスンし、レシーバーの開始によってイベントに対応できます。 この機能により、Service Bus はリアクティブ プログラミング モデルで使用できます。

機能を有効にするには、以下が必要です。

* 1 つ以上の Service Bus キューが含まれた Azure Service Bus Premium 名前空間、または 1 つ以上のサブスクリプションがある Service Bus トピックが含まれた Azure Service Bus Premium 名前空間。
* Azure Service Bus 名前空間への共同作成者アクセス。
* さらに、Service Bus 名前空間の Azure Event Grid サブスクリプションが必要です。 このサブスクリプションは、取得すべきメッセージがあるという通知を Azure Event Grid から受け取ります。 典型的なサブスクライバーとしてはロジック アプリ、Azure 関数、または Web アプリと接続する webhook が考えられます。これらはメッセージを処理します。 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>共同作成者アクセスがあることの確認

Service Bus 名前空間に移動して、次に示すように [アクセス制御 (IAM)] を選択します。

![1][]

### <a name="events-and-event-schemas"></a>イベントとイベント スキーマ

現在の Azure Service Bus では、2 つのシナリオでイベントが送信されます。

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

さらに、標準の Azure Event Grid セキュリティと[認証メカニズム](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication)が使用されます。

Event Grid イベント スキーマの詳細を確認するには、[この](https://docs.microsoft.com/en-us/azure/event-grid/event-schema)リンク先に移動してください。

#### <a name="active-messages-available-event"></a>アクティブなメッセージが利用可能なイベント

このイベントは、キューまたはサブスクリプションにアクティブなメッセージがあり、リッスンしているレシーバーがない場合に生成されます。

このイベントのスキーマは、次のようになります。

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>配信不能なメッセージが利用可能なイベント

配信不能キューごとに少なくとも 1 つのイベントを受信します。このキューには、メッセージはありますがアクティブなレシーバーはありません。

このイベントのスキーマは、次のようになります。

```JSON
[{
     "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>イベントが発行される頻度と発行されるイベントの数

名前空間に複数のキューおよびトピック/サブスクリプションがある場合、キューごとおよびサブスクリプションごとに少なくとも 1 つのイベントを受信します。 イベントは、Service Bus エンティティにメッセージがなく新しいメッセージが届いた場合にすぐに発行されます。または、Azure Service Bus がアクティブなレシーバーを検出するまで 2 分おきに発行されます。 メッセージの読み取りはイベントを中断しません。

既定の Azure Service Bus では、名前空間内のすべてのエンティティについてイベントが発行されます。 特定のエンティティについてのみイベントを取得したい場合、次のフィルターのセクションを参照してください。

### <a name="filtering-limiting-from-where-you-get-events"></a>フィルター (イベントの取得元の制限)

たとえば、名前空間内の 1 つのキューまたはサブスクリプションについてのみイベントを取得したい場合、Azure Event Grid によって提供される "先頭" フィルターまたは "末尾" フィルターを使用できます。 一部のインターフェイスでは、これらのフィルターは "プレ" フィルターおよび "サフィックス" フィルターと呼ばれます。 複数のキューとサブスクリプションについてイベントを取得したい場合、複数の異なる Azure Event Grid サブスクリプションを作成し、それぞれにフィルターを適用できます。

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Service Bus 名前空間の Azure Event Grid サブスクリプションを作成する方法

Service Bus 名前空間に Event Grid サブスクリプションを作成するには、3 つの方法があります。

* [Azure ポータル](#portal-instructions)
* [Azure CLI](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>ポータルの手順

新しい Azure Event Grid サブスクリプションを作成するには、Azure Portal で名前空間に移動して、[イベント グリッド] ブレードを選択します。 + [イベント サブスクリプション] をクリックします。以下には、既にいくつかの Event Grid サブスクリプションがある名前空間が示されています。

![20][]

次のスクリーンショットには、特定のフィルターなしで Azure 関数または webhook をサブスクライブする方法のサンプルが示されています。

![21][]

## <a name="azure-cli-instructions"></a>Azure CLI の手順

最初に、Azure CLI バージョン 2.0 以上がインストールされていることを確認してください。 インストーラーはこちらでダウンロードできます。 次に、"Windows + X" キーを押して、管理者権限で新しい PowerShell コンソールを開きます。 または、Azure Portal 内でコマンド シェルを使用することもできます。

次のコードを実行します。

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>PowerShell の手順

Azure PowerShell がインストールされていることを確認してください。 こちらで見つけることができます。 次に、"Windows + X" キーを押して、管理者権限で新しい PowerShell コンソールを開きます。 または、Azure Portal 内でコマンド シェルを使用することもできます。

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

ここから、他のセットアップ オプションを調べたり、[イベントが送信されていることをテスト](#test-that-events-are-flowing)したりできます。

## <a name="next-steps"></a>次の手順

* Service Bus と Event Grid の[例](service-bus-to-event-grid-integration-example.md)。
* [Azure Event Grid](https://docs.microsoft.com/en-us/azure/azure-functions/) について学習します。
* [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/) について学習します。
* [Azure Logic Apps](https://docs.microsoft.com/en-us/azure/logic-apps/) についての詳細を見る
* Azure Service Bus の詳細については、[こちら](https://docs.microsoft.com/en-us/azure/azure-functions/)を参照してください。

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png