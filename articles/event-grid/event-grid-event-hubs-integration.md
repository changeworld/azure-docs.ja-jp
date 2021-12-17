---
title: チュートリアル:Event Hubs データをデータ ウェアハウスに送信する - Event Grid
description: Azure Functions と Event Grid トリガーを使用して、Event Hubs でキャプチャされたデータを Azure Synapse Analytics に格納する方法について説明します。
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 2887cdc1b4666daf73a21a76907f80d50d345d82
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2021
ms.locfileid: "112414091"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>チュートリアル:ビッグ データをデータ ウェアハウスにストリーミングする
Azure [Event Grid](overview.md) は、アプリとサービスからの通知またはイベントへの対応を可能にするインテリジェントなイベント ルーティング サービスです。 たとえば、これを使用すると、BLOB ストレージまたは Data Lake Storage にキャプチャされた Event Hubs データを処理する Azure 関数をトリガーできます。 この[サンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)は、Event Grid と Azure Functions を使用して、キャプチャされた Event Hubs データを BLOB ストレージから Azure Synapse Analytics (特に、専用 SQL プール) に移行する方法を示しています。

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](./includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>次のステップ

* Azure メッセージング サービスの違いについては、「[メッセージを配信する Azure サービスの選択](compare-messaging-services.md)」を参照してください。
* Event Grid の概要については、[Event Grid の紹介](overview.md)に関する記事を参照してください。
* Event Hubs Capture の概要については、「[Azure Portal を使用して Event Hubs Capture を有効にする](../event-hubs/event-hubs-capture-enable-through-portal.md)」をご覧ください。
* サンプルの設定と実行に関する詳細については、[Event Hubs Capture と Event Grid のサンプル](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo)に関する記事をご覧ください。
