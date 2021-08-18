---
title: Kubernetes 上の Azure Event Grid - バッチ イベントの配信
description: この記事では、イベントのバッチを宛先に配信する方法について説明します。
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: 46ae19af49b827af857f5f224ee5f0013d620a43
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414595"
---
# <a name="event-grid-on-kubernetes---batch-event-delivery"></a>Kubernetes 上の Event Grid - バッチ イベントの配信 
Azure Arc を使用する Kubernetes 上の Event Grid では、1 つの配信要求での複数のイベントの配信がサポートされています。 この機能により、HTTP の要求ごとのオーバーヘッドを発生させることなく、配信全体のスループットを向上させることができます。 バッチ イベント配信は既定ではオフになっており、イベント サブスクリプション構成を使用してオンにすることができます。 

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

> [!WARNING]
> イベント ハンドラー コードでバッチ処理要求ごとに潜在的により多くの作業を行う必要がある場合でも、各配信要求を処理するために許可される最大期間は変わりません。 配信タイムアウトの既定値は 60 秒です。

## <a name="batch-event-delivery-policy"></a>バッチ イベント配信のポリシー
Kubernetes 上の Event Grid でのバッチ イベント配信の動作は、次の 2 つの設定を調整することで、イベント サブスクリプションごとにカスタマイズできます。

- **バッチごとの最大イベント数**
    
    この設定では、バッチ処理される配信要求に追加できるイベントの数の上限が決まります。
- **優先バッチ サイズ (KB 単位)**
    
    この構成項目は、配信要求ごとに送信できる最大キロ バイト数をさらに制御するために使用されます。

## <a name="batch-event-delivery-behavior"></a>バッチ イベント配信の動作   

- **すべてまたはなし**

    Kubernetes 上の Event Grid 操作は、すべてまたはなしのセマンティクスです。 バッチ イベント配信の一部成功はサポートされていません。 イベント ハンドラーは、バッチごとに 60 秒で合理的に処理できるだけの数のイベントのみを要求するように注意する必要があります。
- **オプティミスティック バッチ処理**

    バッチ処理ポリシー設定は、バッチ処理動作に対する厳密な限界ではなく、ベストエフォート ベースで尊重されます。 イベント レートが低い場合、バッチのサイズが、バッチごとのイベントの要求した最大数より小さいことがよくあります。
- **バッチ配信は既定で OFF に設定されています**

    既定では、Kubernetes 上の Event Grid によって、各配信要求に 1 つのイベントのみが追加されます。 バッチでのイベント配信を有効にする方法は、イベント サブスクリプション ペイロードで、この記事で前述した設定のいずれかを指定することです。
- **既定値**

    イベント サブスクリプションを作成するときに、両方の設定 (バッチあたりの最大イベント数とキロバイト単位の概算バッチ サイズ) を指定する必要はありません。 1 つだけ設定すれば、Kubernetes 上の Event Grid によって (構成可能な) 既定値が使用されます。 

## <a name="example"></a>例
次の例は、バッチ処理を有効にするためにエンドポイント プロパティで `maxEventsPerBatch` と `preferredBatchSizeInKilobytes` を設定する方法を示しています。 

```json
{
    "properties":
    {
        "destination":
        {
            "endpointType": "WebHook",
            "properties":
             {
                "endpointUrl": "<your_webhook_url>",
                "maxEventsPerBatch": 10,
                "preferredBatchSizeInKilobytes": 64
             }
        },
    }
}
```

## <a name="next-steps"></a>次の手順
Azure Arc for Kubernetes 上の Event Grid でサポートされる宛先とハンドラーについて詳しくは、[Kubernetes 上の Event Grid - イベント ハンドラー](event-handlers.md)に関する記事を参照してください。