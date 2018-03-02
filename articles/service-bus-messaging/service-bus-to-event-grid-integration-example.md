---
title: "Azure Service Bus と Event Grid の統合の例 | Microsoft Docs"
description: "Service Bus メッセージングと Event Grid の統合の例"
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
ms.openlocfilehash: 2a4d17673340d145de9a3514f920c74f7eebf6b6
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
---
# <a name="azure-service-bus-to-azure-event-grid-examples"></a>Azure Service Bus と Azure Event Grid の例

このドキュメントでは、Event Grid からのイベントの受信に基づいてメッセージを受信する Azure 関数とロジック アプリを設定する方法について説明します。 この例では、2 つのサブスクリプションを含む Service Bus トピックがあり、1 つの Service Bus サブスクリプションのみにイベントを送信するように Event Grid サブスクリプションが作成されることを前提としています。 次に、メッセージを Service Bus トピックに送信します。この Service Bus サブスクリプション用にイベントが生成され、関数またはロジック アプリが Service Bus サブスクリプションからメッセージを受信するのを確認し、完了します。

* 最初に、すべての[前提条件](#prerequisites)を満たしてください。
* Event Grid からのイベントの初期フローをデバッグして確認するための、[簡単なテスト Azure 関数](#test-function-setup)を作成します。  **この手順は、3. または 4. の実行に関係なく行う必要があります。**
* Event Grid イベントに基づいて [Service Bus メッセージを受信して処理する Azure 関数](#receive-messages-using-azure-function)を作成します。
* [Logic Apps](#receive-messages-using-azure-logic-app) を利用します。

## <a name="prerequisites"></a>前提条件

### <a name="service-bus-namespace"></a>Service Bus 名前空間

Service Bus Premium 名前空間を作成します。 2 つのサブスクリプションが含まれた Service Bus トピックを作成します。

### <a name="code-to-send-message-to-the-service-bus-topic"></a>メッセージを Service Bus トピックに送信するコード

メッセージを Service Bus トピックに送信するには、お好きな手段を使用できます。 または、以下のサンプルを使用できます。 サンプル コードでは、Visual Studio 2017 を使用していることが前提とされます。

[この GitHub リポジトリ](https://github.com/Azure/azure-service-bus/)を複製します。

次のフォルダーに移動します。

\samples\DotNet\Microsoft.ServiceBus.Messaging\ServiceBusEventGridIntegration。SBEventGridIntegration.sln というファイルを開きます。

MessageSender プロジェクトに移動し、Program.cs を開きます。

![8][]

トピック名と接続文字列を入力し、コンソール アプリケーションを実行します。

```CSharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
const string TopicName = "YOUR TOPIC NAME";
```

## <a name="test-function-setup"></a>テスト関数のセットアップ

エンド ツー エンドの完全なシナリオを実行する前に、少なくとも小さなテスト関数を用意します。これを使用して、送信されているイベントのデバッグと確認を行えます。

ポータルで、新しい Azure 関数アプリケーションを作成します。 Azure Functions の基本について学習するには、この[リンク先](https://docs.microsoft.com/en-us/azure/azure-functions/)に移動してください。

新しく作成した関数で、小さなプラス記号をクリックして http トリガー関数を追加します。

![2][]

![3][]

次のコードを関数にコピーします。

```CSharp
#r "Newtonsoft.Json"
using System.Net;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");
    // parse query parameter
    var content = req.Content;

    string jsonContent = await content.ReadAsStringAsync(); 
    log.Info($"Received Event with payload: {jsonContent}");

IEnumerable<string> headerValues;
if (req.Headers.TryGetValues("Aeg-Event-Type", out headerValues))
{
var validationHeaderValue = headerValues.FirstOrDefault();
if(validationHeaderValue == "SubscriptionValidation")
{
var events = JsonConvert.DeserializeObject<GridEvent[]>(jsonContent);
     var code = events[0].Data["validationCode"];
     return req.CreateResponse(HttpStatusCode.OK,
     new { validationResponse = code });
}
}

    return jsonContent == null
    ? req.CreateResponse(HttpStatusCode.BadRequest, "Pass a name on the query string or in the request body")
    : req.CreateResponse(HttpStatusCode.OK, "Hello " + jsonContent);
}

public class GridEvent
{
    public string Id { get; set; }
    public string EventType { get; set; }
    public string Subject { get; set; }
    public DateTime EventTime { get; set; }
    public Dictionary<string, string> Data { get; set; }
    public string Topic { get; set; }
}
```

[保存および実行] をクリックします。

## <a name="connect-function-and-namespace-via-event-grid"></a>Event Grid による関数と名前空間の接続

次の手順では、関数と Service Bus 名前空間を関連付けます。 たとえば、Azure Portal を使用します。 PowerShell または Azure CLI を使用して同じことを実現する方法を確認するには、[コンセプト](service-bus-to-event-grid-integration-concept.md に関するページを参照してください。

新しい Azure Event Grid サブスクリプションを作成するには、Azure Portal で名前空間に移動して、[イベント グリッド] ブレードを選択します。 + [イベント サブスクリプション] をクリックします。

次のスクリーンショットには名前空間が示されています。ここには、既にいくつかの Event Grid サブスクリプションがあります。

![20][]

次のスクリーンショットには、特定のフィルターなしで Azure 関数または webhook をサブスクライブする方法が示されています。 必ず Service Bus サブスクリプションの関連フィルターを "サフィックス フィルター" として追加してください。

![21][]

前提条件で説明したとおりメッセージを Service Bus トピックに送信し、Azure 関数の監視機能を使用して、イベントが送信されていることを確認します。

![9][]

### <a name="receive-messages-using-azure-function"></a>Azure 関数を使用したメッセージの受信

前のセクションでは、テストとデバッグの簡単なシナリオを確認し、イベントが送信されていることを確認しました。 ドキュメントのこの部分では、イベントを受信した後にメッセージを受信して処理する方法について確認します。

次の方法で Azure 関数を追加するのは、Azure Functions 内の Service Bus 関数では新しい Event Grid 統合がまだネイティブにサポートされていないためです。

前提条件で開いたのと同じ Visual Studio ソリューションで、ReceiveMessagesOnEvent.cs を選択します。 接続文字列をコードに入力します。

![10][]

```Csharp
const string ServiceBusConnectionString = "YOUR CONNECTION STRING";
```

Azure Portal に移動し、前に「[2. テスト関数のセットアップ](#2-test-function-setup)」で作成した Azure 関数の発行プロファイルをダウンロードします。

![11][]

次に、Visual Studio で SBEventGridIntegration を右クリックして [発行] を選択します。 前にダウンロードした発行プロファイルを使用して、[プロファイルのインポート] を選択して [発行] をクリックします。

![12][]

新しい Azure 関数を発行した後、その新しい Azure 関数を指す新しい Azure Event Grid サブスクリプションを作成します。 適切な "末尾" フィルターを適用してください。これは、Service Bus サブスクリプション名です。

次に、前に作成した Azure Service Bus トピックにメッセージを送信し、ポータルの Azure 関数ログで、イベントが送信されていることとメッセージが受信されたことを確認します。

![12-1][]

### <a name="receive-messages-using-azure-logic-app"></a>Azure ロジック アプリを使用したメッセージの受信

次の手順では、Azure ロジック アプリを Azure Service Bus と Azure Event Grid に接続する方法について説明します。

最初に、Azure Portal で新しいロジック アプリを作成し、Event Grid を開始アクションとして選択します。

![13][]

Logic Apps デザイナーの初期ビューは次のスクリーンショットのようになりますが、[リソース名] を独自の名前空間名に置き換える必要があります。 さらに、詳細オプションを展開し、サブスクリプションのサフィックス フィルターを追加してください。

![14][]

次に、トピック サブスクリプションから受信する Service Bus 受信アクションを追加します。 最後のアクションは、次のスクリーンショットのようになります。

![15][]

次に、完了イベントを追加します。これは次のようになります。

![16][]

ロジック アプリを保存し、前提条件で説明したとおり Service Bus トピックにメッセージを送信します。 次に、ロジック アプリの実行を確認します。 [概要] をクリックして [実行の履歴] に移動すると、実行についてさらにデータを取得することもできます。

![17][]

![18][]

## <a name="next-steps"></a>次の手順

* [Azure Event Grid](https://docs.microsoft.com/azure/event-grid/) について学習します。
* [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) について学習します。
* [Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) についての詳細を見る
* Azure Service Bus の詳細については、[こちら](https://docs.microsoft.com/azure/service-bus/)を参照してください。

[2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid2.png
[3]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid3.png
[7]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid7.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[10]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid10.png
[11]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid11.png
[12]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12.png
[12-1]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-1.png
[12-2]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid12-2.png
[13]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid13.png
[14]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid14.png
[15]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid15.png
[16]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid16.png
[17]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid17.png
[18]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid18.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
