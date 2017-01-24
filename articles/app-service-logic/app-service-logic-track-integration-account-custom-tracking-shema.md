---
title: "カスタム追跡スキーマ | Microsoft Docs"
description: "カスタム追跡スキーマについての詳細情報"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d88fa57c2f343636d7529780dc1b27ccb604ee02
ms.openlocfilehash: 0de8128b01f760340e85078b433707c566fa2e4f


---
# <a name="custom-tracking-schemas"></a>カスタム追跡スキーマ
Azure 統合アカウントでは、カスタム追跡スキーマを使用して企業間 (B2B) 取引の監視に役立てることができます。

## <a name="custom-tracking-schema"></a>カスタム追跡スキーマ
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| プロパティ | 型 | Description |
| --- | --- | --- |
| sourceType |   | 実行ソースのタイプ。 許可されている値は、**Microsoft.Logic/workflows** と **custom** です。 (必須) |
| から |   | ソースのタイプが **Microsoft.Logic/workflows** である場合は、このスキーマの後にソース情報を続ける必要があります。 ソースのタイプが **custom** の場合、スキーマは JToken です。 (必須) |
| systemId | String | ロジック アプリのシステム ID。 (必須) |
| runId | String | ロジック アプリの実行 ID。 (必須) |
| operationName | String | 操作の名前 (アクションやトリガーなど)。 (必須) |
| repeatItemScopeName | String | アクションが `foreach`/`until` ループ内にある場合の繰り返し項目名。 (必須) |
| repeatItemIndex | 整数 | アクションが `foreach`/`until` ループ内にあるかどうか。 繰り返される項目のインデックスを示します。 (必須) |
| trackingId | String | 追跡 ID (メッセージを関連付けるために使用します)。 (省略可能) |
| correlationId | String | 関連付け ID (メッセージを関連付けるために使用します)。 (省略可能) |
| clientRequestId | String | クライアントがメッセージを関連付けるために設定できます。 (省略可能) |
| eventLevel |   | イベントのレベル。 (必須) |
| eventTime |   | イベントの時刻 (YYYY-MM-DDTHH:MM:SS.00000Z という UTC 形式で示します)。 (必須) |
| recordType |   | 追跡レコードのタイプ。 許可されている値は **custom** です。 (必須) |
| record |   | カスタム レコード タイプ。 許可されている形式は、JToken です。 (必須) |

## <a name="b2b-protocol-tracking-schemas"></a>B2B プロトコル追跡スキーマ
B2B プロトコル追跡スキーマ について詳しくは、次のページをご覧ください。
* [AS2 の追跡スキーマ](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [X12 の追跡スキーマ](app-service-logic-track-integration-account-x12-tracking-shemas.md)

## <a name="next-steps"></a>次のステップ
* [B2B メッセージの監視方法](app-service-logic-monitor-b2b-message.md)についての詳細情報。   
* [Operations Management Suite ポータルでの B2B メッセージ追跡](app-service-logic-track-b2b-messages-omsportal.md)についての詳細情報。
* [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md) についての詳細情報。



<!--HONumber=Dec16_HO3-->


