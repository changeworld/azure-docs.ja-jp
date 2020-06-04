---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Azure 関数
description: Event Grid イベントのイベント ハンドラーとして Azure Functions を使用する方法について説明します。
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0152a9fc11562744f83ab9d20466a3dcc8e2e6e0
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800428"
---
# <a name="azure-function-as-an-event-handler-for-event-grid-events"></a>Event Grid イベントに対するイベント ハンドラーとしての Azure 関数

イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーでは、イベントを処理するアクションが実行されます。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Functions** はその 1 つです。 

サーバーレス アーキテクチャで Event Grid からのイベントに応答するには、**Azure Functions** を使用します。 ハンドラーとして Azure 関数を使用する場合は、汎用 HTTP トリガーではなく Event Grid トリガーを使用します。 Event Grid では、Event Grid トリガーが自動的に検証されます。 汎用 HTTP トリガーの場合は、[検証応答](webhook-event-delivery.md)を開発者自身が実装する必要があります。

関数での Event Grid トリガーの使用の詳細については、「[Azure Functions の Event Grid トリガー](../azure-functions/functions-bindings-event-grid.md)」を参照してください。

## <a name="tutorials"></a>チュートリアル

|タイトル  |説明  |
|---------|---------|
| [クイック スタート: 関数を使用したイベントの処理](custom-event-to-function.md) | 処理するカスタム イベントを関数に送信します。 |
| [チュートリアル: Event Grid を使用して、アップロードされたイメージのサイズ変更を自動化する](resize-images-on-storage-blob-upload-event.md) | ユーザーは、Web アプリを使ってストレージ アカウントにイメージをアップロードします。 ストレージ BLOB が作成されると、Event Grid によって関数アプリにイベントが送信され、アップロードされたイメージのサイズが変更されます。 |
| [チュートリアル: ビッグ データをデータ ウェアハウスにストリーミングする](event-grid-event-hubs-integration.md) | Event Hubs によってキャプチャ ファイルが作成されると、Event Grid が関数アプリにイベントを送信します。 アプリは Capture ファイルを取得し、データ ウェアハウスにデータを移行します。 |
| [チュートリアル:Azure Service Bus の Azure Event Grid への統合の例](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Event Grid では、Service Bus トピックからのメッセージが、関数アプリとロジック アプリに送信されます。 |

## <a name="rest-example-for-put"></a>REST の例 (PUT 用)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "AzureFunction",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Web/sites/<FUNCTION APP NAME>/functions/<FUNCTION NAME>",
                "maxEventsPerBatch": 1,
                "preferredBatchSizeInKilobytes": 64
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 
