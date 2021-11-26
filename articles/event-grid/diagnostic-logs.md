---
title: Azure Event Grid - Azure Event Grid トピックと Event Grid ドメインの診断ログ
description: この記事では、Azure Event Grid のトピックまたはドメインの診断ログの概念に関する情報を提供します。
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: df3fe9eaab544e3e52ff3a2da24fe7b624292367
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546825"
---
# <a name="diagnostic-logs-for-event-grid-topics-and-event-grid-domains"></a>Event Grid のトピックおよび Event Grid ドメインの診断ログ

Event Grid ユーザーは、診断設定を使用して、Azure ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースのいずれかで **発行および配信エラー** ログをキャプチャして表示することができます。 この記事では、ログのスキーマとログ エントリの例を示します。

## <a name="schema-for-publishdelivery-failure-logs"></a>発行および配信エラー ログのスキーマ

| プロパティ名 | データ型 | 説明 |
| ------------- | --------- | ----------- |
| Time | DateTime | ログ エントリが生成された時刻 <p>**値の例:** 01-29-2020 09:52:02.700</p> |
| EventSubscriptionName | String | イベント サブスクリプションの名前 <p>**値の例:** "EVENTSUB1"</p> <p>このプロパティは、配信エラー ログにのみ存在します。</p>  |
| カテゴリ | String | ログのカテゴリの名前。 <p>**値の例:** "DeliveryFailures" または "PublishFailures" | 
| OperationName | String | エラーの原因となった操作の名前。<p>**サンプル値:** 配信に失敗した場合は、"Deliver"。 |
| Message | String | エラーの理由とその他の詳細情報を説明する、ユーザーのログ メッセージ。 |
| ResourceId | String | トピックまたはドメイン リソースのリソース ID<p>**サンプル値:** `/SUBSCRIPTIONS/SAMPLE-SUBSCRIPTION-ID/RESOURCEGROUPS/SAMPLE-RESOURCEGROUP/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/TOPIC1` |

## <a name="example---schema-for-publishdelivery-failure-logs"></a>例 - 発行および配信エラー ログのスキーマ

```json
{
    "time": "2019-11-01T00:17:13.4389048Z",
    "resourceId": "/SUBSCRIPTIONS/SAMPLE-SUBSCTIPTION-ID /RESOURCEGROUPS/SAMPLE-RESOURCEGROUP-NAME/PROVIDERS/MICROSOFT.EVENTGRID/TOPICS/SAMPLE-TOPIC-NAME ",
    "eventSubscriptionName": "SAMPLEDESTINATION",
    "category": "DeliveryFailures",
    "operationName": "Deliver",
    "message": "Message:outcome=NotFound, latencyInMs=2635, id=xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx, systemId=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx, state=FilteredFailingDelivery, deliveryTime=11/1/2019 12:17:10 AM, deliveryCount=0, probationCount=0, deliverySchema=EventGridEvent, eventSubscriptionDeliverySchema=EventGridEvent, fields=InputEvent, EventSubscriptionId, DeliveryTime, State, Id, DeliverySchema, LastDeliveryAttemptTime, SystemId, fieldCount=, requestExpiration=1/1/0001 12:00:00 AM, delivered=False publishTime=11/1/2019 12:17:10 AM, eventTime=11/1/2019 12:17:09 AM, eventType=Type, deliveryTime=11/1/2019 12:17:10 AM, filteringState=FilteredWithRpc, inputSchema=EventGridEvent, publisher=DIAGNOSTICLOGSTEST-EASTUS.EASTUS-1.EVENTGRID.AZURE.NET, size=363, fields=Id, PublishTime, SerializedBody, EventType, Topic, Subject, FilteringHashCode, SystemId, Publisher, FilteringTopic, TopicCategory, DataVersion, MetadataVersion, InputSchema, EventTime, fieldCount=15, url=sb://diagnosticlogstesting-eastus.servicebus.windows.net/, deliveryResponse=NotFound: The messaging entity 'sb://diagnosticlogstesting-eastus.servicebus.windows.net/eh-diagnosticlogstest' could not be found. TrackingId:c98c5af6-11f0-400b-8f56-c605662fb849_G14, SystemTracker:diagnosticlogstesting-eastus.servicebus.windows.net:eh-diagnosticlogstest, Timestamp:2019-11-01T00:17:13, referenceId: ac141738a9a54451b12b4cc31a10dedc_G14:"
}
```

`Outcome` に指定できる値は、`NotFound`、`Aborted`、`TimedOut`、`GenericError`、`Busy` です。 Event Grid では、`message` のイベント ハンドラーから受信したあらゆる情報をログに記録します。 たとえば、`GenericError` の場合、HTTP ステータス コード、エラー コード、エラー メッセージをログに記録します。

## <a name="schema-for-data-plane-requests"></a>データ プレーン要求のスキーマ

| プロパティ名 | データ型 | 説明 |
| ------------- | --------- | ----------- |
| NetworkAccess | String | **パブリック アクセス**  - パブリック IP を介した接続の場合 <br /> **プライベート アクセス** - プライベート リンクを介した接続の場合 |
| ClientIpAddress | String | 受信要求のソース IP |
| TlsVersion | String | クライアント接続で使用される Tls バージョン。 指定できる値は、**1.0**、**1.1**、**1.2** です。 |
| Authentication/Type | String | メッセージの発行時に認証のために使用されるシークレットのタイプ。 <br /> **[不明]** – 他の認証タイプではありません。 OPTIONS 要求は、この認証タイプを持ちます <br /> **キー** – 要求は SAS キーを使用します <br /> **SASToken** – 要求は SAS キーから生成された SAS トークンを使用します。 <br /> **AADAccessToken** – AAD によって発行された JWT トークン |
| Authentication/ObjectId | String | サービス プリンシパルの ObjectId は、AADAccessToken 認証タイプを使用します。 |
| OperationResult | String | 発行の結果。 **Success**、**Unauthorized**, **Forbidden**、**RequestEntityTooLarge**、**BadRequest** & **InternalServerError** |
| TotalOperations | String | これらのトレースは、発行要求ごとには生成されません。 上記の値の一意の組み合わせごとに集計が 1 分ごとに出力されます |

## <a name="example---schema-for-data-plane-requests"></a>例 - データ プレーン要求のスキーマ

```json
{
    "time": "2021-10-26T21:44:16.8117322Z",
    "resourceId": "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/RESOURCEGROUPS/BMT-TEST/PROVIDERS/MICROSOFT.EVENTGRID/DOMAINS/BMTAUDITLOGDOMAIN",
    "operationName": "Microsoft.EventGrid/events/send",
    "category": "DataPlaneRequests",
    "level": "Information",
    "region": "CENTRALUSEUAP",
    "properties": {
        "aggregatedRequests": [
            {
                "networkAccess": "PublicAccess",
                "clientIpAddress": "xx.xx.xx.xxx",
                "tlsVersion": "1.2",
                "authentication": {
                            "type": "AADAccessToken",
                            "objectId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx"
                },
                "operationResult": "Success",
                "totalOperations": 1
            }
        ]
    }
}
```

`DataPlaneRequests` 診断設定を選択すると、Event Grid リソースにより、パブリック アクセスとプライベート アクセスを含むデータ プレーン操作の監査トレースの発行が開始されます。このトレースでは、必要に応じて 1 つ以上の要求がログに記録される場合があります。

## <a name="next-steps"></a>次のステップ

トピックまたはドメインの診断ログを有効にする方法については、[診断ログの有効化](enable-diagnostic-logs-topic.md)に関するページを参照してください。
