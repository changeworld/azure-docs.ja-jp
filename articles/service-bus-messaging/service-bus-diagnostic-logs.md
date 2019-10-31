---
title: Azure Service Bus 診断ログ | Microsoft Docs
description: Service Bus の診断ログを設定する方法について説明します。
keywords: ''
documentationcenter: .net
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 41e0bdc1f04c9491ebe939f46b59ae4eb2bc7ab6
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592455"
---
# <a name="enable-diagnostic-logs-for-service-bus"></a>Service Bus の診断ログを有効にする

Azure Service Bus 名前空間の使用を開始するとき、名前空間がいつ、どのように作成、削除、またはアクセスされるかを監視したい場合があります。 この記事では、使用可能なすべての操作/診断ログの概要について説明します。

Azure Service Bus は現在、Azure Service Bus 名前空間上で実行される**管理操作**をキャプチャするアクティビティ/操作ログをサポートしています。 具体的には、これらのログは操作の種類 (キューの作成、使用リソース、操作の状態など) をキャプチャします。

## <a name="operational-logs-schema"></a>操作ログのスキーマ

すべてのログは、次の 2 つの場所に JavaScript Object Notation (JSON) 形式で格納されます。

- **AzureActivity** - ポータル上で、または Azure Resource Manager テンプレートのデプロイを使用して、名前空間に対して実行された操作/アクションのログを表示します。
- **AzureDiagnostics** - API を使用して、または言語 SDK 上の管理クライアントを使用して、名前空間に対して実行された操作/アクションのログを表示します。

操作ログの JSON 文字列には、次の表に示す要素が含まれます。

| 名前 | 説明 |
| ------- | ------- |
| ActivityId | 指定されたアクティビティを識別するために使用される内部 ID |
| EventName | 操作の名前 |
| ResourceId | Azure Resource Manager リソース ID |
| SubscriptionId | サブスクリプション ID |
| EventTimeString | 操作時間 |
| EventProperties | 操作プロパティ |
| Status | 操作の状態 |
| Caller | 操作の呼び出し元 (Azure Portal または管理クライアント) |
| Category | OperationalLogs |

操作ログの JSON 文字列の例を次に示します。

```json
{
  "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
  "EventName": "Create Queue",
  "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.SERVICEBUS/NAMESPACES/SHOEBOXEHNS-CY4001",
  "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
  "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
  "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
  "Status": "Succeeded",
  "Caller": "ServiceBus Client",
  "category": "OperationalLogs"
}
```

## <a name="what-eventsoperations-are-captured-in-operational-logs"></a>操作ログでキャプチャされるイベント/操作の種類

操作ログでは、Azure Service Bus 名前空間上で実行されるすべての管理操作がキャプチャされます。 Azure Service Bus 上では大量のデータ操作が実行されるため、データ操作はキャプチャされません。

> [!NOTE]
> データ操作をより適切に追跡するために、クライアント側のトレースを使用することをお勧めします。

操作ログでは、次の管理操作がキャプチャされます。 

| Scope (スコープ) | Operation|
|-------| -------- |
| 名前空間 | <ul> <li> 名前空間の作成</li> <li> 名前空間の更新 </li> <li> 名前空間の削除 </li>  </ul> | 
| キュー | <ul> <li> キューの作成</li> <li> キューの更新</li> <li> キューの削除 </li> </ul> | 
| トピック | <ul> <li> トピックの作成 </li> <li> トピックの更新 </li> <li> トピックの削除 </li> </ul> |
| Subscription | <ul> <li> サブスクリプションの作成 </li> <li> サブスクリプションの更新 </li> <li> サブスクリプションの削除 </li> </ul> |

> [!NOTE]
> 現在、**読み取り**操作は操作ログで追跡されません。

## <a name="how-to-enable-operational-logs"></a>操作ログを有効にする方法

操作ログは、既定では無効になっています。 診断ログを有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) で、Azure Service Bus 名前空間に移動し、 **[監視]** で **[診断設定]** をクリックします。

   ![ブレードで診断ログに移動する](./media/service-bus-diagnostic-logs/image1.png)

2. **[診断設定の追加]** をクリックして診断設定を構成します。  

   ![診断ログを有効にする](./media/service-bus-diagnostic-logs/image2.png)

3. 診断設定を構成します。
   1. 診断設定を識別するための**名前**を入力します。
   2. 診断の宛先を選択します。
      - **[ストレージ アカウント]** を選択する場合は、診断が格納されるストレージ アカウントを構成する必要があります。
      - **[Event hubs] (イベント ハブ)** を選択する場合は、診断設定のストリーミング先の適切なイベント ハブを構成する必要があります。
      - **[Log Analytics]** を選択する場合は、診断が送信される Log Analytics のインスタンスを指定する必要があります。
    3. **[OperationalLogs]** を確認します。

       ![診断ログの状態を変更する](./media/service-bus-diagnostic-logs/image3.png)

4. **[Save]** をクリックします。


新しい設定は、10 分ほどで有効になります。 その後、構成したアーカイブ ターゲットのログが **[診断ログ]** ブレードに表示されます。

診断の構成の詳細については、[Azure 診断ログの概要](../azure-monitor/platform/diagnostic-logs-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次の手順

Service Bus の詳細については、次のリンクを参照してください。

* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus の使用](service-bus-dotnet-get-started-with-queues.md)
