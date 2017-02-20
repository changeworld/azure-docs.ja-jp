---
title: "Azure Event Hubs 診断ログ | Microsoft Docs"
description: "Microsoft Azure で Event Hubs の診断ログを分析する方法について説明します。"
keywords: 
documentationcenter: 
services: event-hubs
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 4d7d0e1f5ffb395bf91bbdac1ab4b9ec3f9dee1c
ms.openlocfilehash: cb8c7930ee423543c91366de64220ee55609a4cf


---
# <a name="event-hub-diagnostic-logs"></a>Event Hubs 診断ログ

## <a name="introduction"></a>はじめに
Event Hubs には&2; 種類のログがあります。 
* [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs): 常に有効で、ジョブで実行される操作の洞察が示されます。
* [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs): ユーザーが構成でき、作成/更新時、実行中、および削除されるまでに開始されたジョブで発生している、あらゆる事象に関する詳細な洞察が示されます。

## <a name="how-to-enable-diagnostic-logs"></a>診断ログを有効にする方法
既定では、診断ログは**オフ**になっています。 有効にするには、次の手順に従います。

Azure Portal にサインオンし、ストリーミング ジョブ ブレードに移動して、[監視] の下にある [診断ログ] ブレードを使用します。

![ブレードで診断ログに移動する](./media/event-hubs-diagnostic-logs/image1.png)  

次に、[診断を有効にする] リンクをクリックします

![診断ログを有効にする](./media/event-hubs-diagnostic-logs/image2.png)

開いている診断で、状態を [オン] に変更します。

![診断ログの状態を変更する](./media/event-hubs-diagnostic-logs/image3.png)

必要なアーカイブ ターゲット (ストレージ アカウント、イベント ハブ、Log Analytics) を構成し、収集するログのカテゴリ (実行、作成) を選択します。 次に、新しい診断の構成を保存します。

保存された構成が有効になるには約 10 分かかります。構成が有効になると、[診断ログ] ブレードの構成済みアーカイブ ターゲットにログが表示されます。

診断の構成の詳細については、[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)に関するページをご覧ください。

## <a name="diagnostic-logs-categories"></a>診断ログのカテゴリ
現時点で取得できる診断ログのカテゴリは&2; つあります。

* **ArchivalLogs:** Event Hubs アーカイブ、特にアーカイブ エラーに関連するログを取得します。
* **OperationalLogs:** Event Hubs 操作中に発生したこと、特に、Event Hub 作成、使用リソース、操作の状態などの操作の種類を取得します。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ
すべてのログが JSON 形式で格納され、各エントリには次の形式の文字列フィールドが含まれます。


### <a name="archive-error-schema"></a>アーカイブ エラー スキーマ
名前 | Description
------- | -------
TaskName | 失敗したタスクの説明
ActivityId | 追跡目的の内部 ID
trackingId | 追跡目的の内部 ID
resourceId | ARM リソース ID
eventHub | Event Hub の完全名 (名前空間の名前を含む)
partitionId | Event Hub 内に書き込まれるパーティション
archiveStep | ArchiveFlushWriter
startTime | 障害開始時刻
failures | 障害が発生した回数
durationInSeconds | 障害の時間
Message | エラー メッセージ
カテゴリ | ArchiveLogs

#### <a name="example-archive-log"></a>アーカイブ ログの例

```json
{
     "TaskName": "EventHubArchiveUserError",
     "ActivityId": "21b89a0b-8095-471a-9db8-d151d74ecf26",
     "trackingId": "21b89a0b-8095-471a-9db8-d151d74ecf26_B7",
     "resourceId": "/SUBSCRIPTIONS/854D368F-1828-428F-8F3C-F2AFFA9B2F7D/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
     "eventHub": "fbettati-opera-eventhub:eventhub:eh123~32766",
     "partitionId": "1",
     "archiveStep": "ArchiveFlushWriter",
     "startTime": "9/22/2016 5:11:21 AM",
     "failures": 3,
     "durationInSeconds": 360,
     "message": "Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (404) Not Found. ---> System.Net.WebException: The remote server returned an error: (404) Not Found.\r\n   at Microsoft.WindowsAzure.Storage.Shared.Protocol.HttpResponseParsers.ProcessExpectedStatusCodeNoException[T](HttpStatusCode expectedStatusCode, HttpStatusCode actualStatusCode, T retVal, StorageCommandBase`1 cmd, Exception ex)\r\n   at Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob.<PutBlockImpl>b__3e(RESTCommand`1 cmd, HttpWebResponse resp, Exception ex, OperationContext ctx)\r\n   at Microsoft.WindowsAzure.Storage.Core.Executor.Executor.EndGetResponse[T](IAsyncResult getResponseResult)\r\n   --- End of inner exception stack trace ---\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.StorageAsyncResult`1.End()\r\n   at Microsoft.WindowsAzure.Storage.Core.Util.AsyncExtensions.<>c__DisplayClass4.<CreateCallbackVoid>b__3(IAsyncResult ar)\r\n--- End of stack trace from previous location where exception was thrown ---\r\n   at System.",
     "category": "ArchiveLogs"
}
```

### <a name="operation-logs-schema"></a>操作ログ スキーマ
名前 | Description
------- | -------
ActivityId | 追跡目的の内部 ID
EventName | 操作の名前           
resourceId | ARM リソース ID
SubscriptionId | サブスクリプション ID
EventTimeString | 操作時間
EventProperties | 操作プロパティ
状態 | 操作の状態
Caller | 操作の呼び出し元 (ポータルまたは管理クライアント)
カテゴリ | OperationalLogs

#### <a name="example-operation-log"></a>操作ログの例

```json
Example: 
{
     "ActivityId": "6aa994ac-b56e-4292-8448-0767a5657cc7",
     "EventName": "Create EventHub",
     "resourceId": "/SUBSCRIPTIONS/1A2109E3-9DA0-455B-B937-E35E36C1163C/RESOURCEGROUPS/DEFAULT-SERVICEBUS-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/SHOEBOXEHNS-CY4001",
     "SubscriptionId": "1a2109e3-9da0-455b-b937-e35e36c1163c",
     "EventTimeString": "9/28/2016 8:40:06 PM +00:00",
     "EventProperties": "{\"SubscriptionId\":\"1a2109e3-9da0-455b-b937-e35e36c1163c\",\"Namespace\":\"shoeboxehns-cy4001\",\"Via\":\"https://shoeboxehns-cy4001.servicebus.windows.net/f8096791adb448579ee83d30e006a13e/?api-version=2016-07\",\"TrackingId\":\"5ee74c9e-72b5-4e98-97c4-08a62e56e221_G1\"}",
     "Status": "Succeeded",
     "Caller": "ServiceBus Client",
     "category": "OperationalLogs"
}
```

## <a name="next-steps"></a>次のステップ
* [Event Hubs の概要](event-hubs-what-is-event-hubs.md)
* [Event Hubs API 概要](event-hubs-api-overview.md)
* [Event Hubs の使用](event-hubs-csharp-ephcs-getstarted.md)


<!--HONumber=Feb17_HO1-->


