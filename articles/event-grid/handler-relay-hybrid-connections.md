---
title: Azure Event Grid イベントに対するイベント ハンドラーとしての Relay ハイブリッド接続
description: Azure Event Grid イベントのイベント ハンドラーとして Azure Relay ハイブリッド接続を使用する方法について説明します。
ms.topic: conceptual
ms.date: 09/28/2021
ms.openlocfilehash: fb36e391663ee53729175a2768d765521b03f4da
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214942"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Azure Event Grid イベントに対するイベント ハンドラーとしての Relay ハイブリッド接続
イベント ハンドラーは、イベントの送信先となる場所です。 ハンドラーは、さらにいくつかのアクションを行ってイベントを処理します。 一部の Azure サービスは、イベントを処理するように自動的に構成されます。**Azure Relay** はその 1 つです。 

Azure **Relay ハイブリッド接続** を使用して、エンタープライズ ネットワーク内にあり、パブリックにアクセスできるエンドポイントがないアプリケーションにイベントを送信します。

## <a name="tutorials"></a>チュートリアル
イベント ハンドラーとして Azure Relay ハイブリッド接続を使用する例については、次のチュートリアルを参照してください。 

|タイトル  |説明  |
|---------|---------|
| [チュートリアル: ハイブリッド接続にイベントを送信する](custom-event-to-hybrid-connection.md) | リスナー アプリケーションで処理するために既存のハイブリッド接続にカスタム イベントを送信します。 |

## <a name="rest-example-for-put"></a>REST の例 (PUT 用)

```json
{
    "properties": 
    {
        "destination": 
        {
            "endpointType": "HybridConnection",
            "properties": 
            {
                "resourceId": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.Relay/namespaces/<RELAY NAMESPACE NAME>/hybridconnections/<HYBRID CONNECTION NAME>"
            }
        },
        "eventDeliverySchema": "EventGridSchema"
    }
}
```

## <a name="next-steps"></a>次のステップ
サポートされているイベント ハンドラーの一覧については、「[イベント ハンドラー](event-handlers.md)」を参照してください。 