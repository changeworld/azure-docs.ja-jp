---
title: "Azure Service Bus 診断ログ | Microsoft Docs"
description: "Microsoft Azure で Service Bus の診断ログを分析する方法について説明します。"
keywords: 
documentationcenter: 
services: service-bus-messaging
author: banisadr
manager: 
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/17/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 90321171586110a3b60c3df5b749003ebbf70ec9
ms.openlocfilehash: 70205b33e9d52e41f5c1a637fee4da192e2a971a
ms.lasthandoff: 02/22/2017


---
# <a name="service-bus-diagnostic-logs"></a>Service Bus の診断ログ

## <a name="introduction"></a>はじめに
Service Bus には&2; 種類のログがあります。 
* [アクティビティ ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs): 常に有効で、ジョブで実行される操作の洞察が示されます。
* [診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs): ユーザーが構成でき、作成/更新時、実行中、および削除されるまでに開始されたジョブで発生している、あらゆる事象に関する詳細な洞察が示されます。

## <a name="how-to-enable-diagnostic-logs"></a>診断ログを有効にする方法
既定では、診断ログは**オフ**になっています。 有効にするには、次の手順に従います。

Azure Portal にサインオンし、ストリーミング ジョブ ブレードに移動して、[監視] の下にある [診断ログ] ブレードを使用します。

![ブレードで診断ログに移動する](./media/service-bus-diagnostic-logs/image1.png)  

次に、[診断を有効にする] リンクをクリックします

![診断ログを有効にする](./media/service-bus-diagnostic-logs/image2.png)

開いている診断で、状態を [オン] に変更します。

![診断ログの状態を変更する](./media/service-bus-diagnostic-logs/image3.png)

必要なアーカイブ ターゲット (ストレージ アカウント、イベント ハブ、Log Analytics) を構成し、収集するログのカテゴリ (実行、作成) を選択します。 次に、新しい診断の構成を保存します。

保存された構成が有効になるには約 10 分かかります。構成が有効になると、[診断ログ] ブレードの構成済みアーカイブ ターゲットにログが表示されます。

診断の構成の詳細については、[診断ログ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)に関するページをご覧ください。

## <a name="diagnostic-logs-schema"></a>診断ログのスキーマ

すべてのログが JSON 形式で格納され、各エントリには次の形式の文字列フィールドが含まれます。

### <a name="operation-logs"></a>操作ログ

操作ログは、Service Bus の操作中に発生したこと、具体的には、キューの作成などの操作の種類、使用されたリソース、操作の状態を取得します。

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

## <a name="next-steps"></a>次のステップ
* [Service Bus の概要](service-bus-messaging-overview.md)
* [Service Bus の使用](service-bus-create-namespace-portal.md)

