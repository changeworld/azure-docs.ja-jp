---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 11/09/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: d828a0c3eb2582a833ee8ad07bdf4f18002c9dca
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2020
ms.locfileid: "94427197"
---
## <a name="trusted-microsoft-services"></a>信頼できる Microsoft サービス
**[Allow trusted Microsoft services to bypass this firewall]\(信頼された Microsoft サービスがこのファイアウォールをバイパスすることを許可する\)** 設定を有効にした場合、次のサービスに Event Hubs リソースへのアクセス権が与えられます。

| 信頼できるサービス | サポートされる使用シナリオ | 
| --------------- | ------------------------- | 
| Azure Event Grid | Event Hubs 名前空間のイベント ハブにイベントを送信することを Azure Event Grid に許可します。 次の手順も行う必要があります。 <ul><li>トピックまたはドメインに対してシステム割り当て ID を有効にする</li><li>Event Hubs 名前空間で Azure Event Hubs データ送信者ロールに ID を追加する</li><li>次に、システムによって割り当てられた ID を使用するために、イベント ハブをエンドポイントとして使用するイベント サブスクリプションを構成する</li></ul> <p>詳細については、「[マネージド ID を使用したイベント配信](../articles/event-grid/managed-service-identity.md)」を参照してください</p>|
| Azure Monitor (診断設定) | Event Hubs 名前空間のイベント ハブに診断情報を送信することを Azure Monitor に許可します。 |
| Azure Stream Analytics | Event Hubs 名前空間のイベント ハブからのデータの読み取り ([入力](../articles/stream-analytics/stream-analytics-add-inputs.md)) またはイベント ハブへの書き込み ([出力](../articles/stream-analytics/event-hubs-output.md)) を Azure Stream Analytics ジョブに許可します。 |
| Azure IoT Hub | Event Hubs 名前空間のイベント ハブにメッセージを送信することを IoT Hub に許可します。 次の手順も行う必要があります。 <ul><li>IoT ハブのシステム割り当て ID を有効にする</li><li>Event Hubs 名前空間で Azure Event Hubs データ送信者ロールに ID を追加する。</li><li>その後、ID ベースの認証を使用するため、イベント ハブをカスタム エンドポイントとして使用するように IoT Hub を構成する。</li></ul>
