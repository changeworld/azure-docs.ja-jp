---
title: インクルード ファイル
description: インクルード ファイル
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 02/26/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ab4c5b98ed9f6fcc8c271797db2d81dcc7ec4449
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755812"
---
次の表に、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) に固有のクォータと制限を示します。 Event Hubs の価格については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

| 制限 | Scope (スコープ) | メモ | 値 |
| --- | --- | --- | --- | --- |
| 名前空間あたりのイベント ハブの数 |名前空間 |以後、新しいイベント ハブの作成要求は拒否されます。 |10 |
| イベント ハブあたりのパーティションの数 |エンティティ |- |32 |
| イベント ハブあたりのコンシューマー グループの数 |エンティティ |- |20 |
| 名前空間あたりの AMQP 接続の数 |名前空間 |追加の接続に関する後続の要求は拒否され、呼び出し元のコードが例外を受け取ります。 |5,000 |
| Event Hubs イベントの最大サイズ|エンティティ |- |256 KB |
| イベント ハブ名の最大サイズ |エンティティ |- |50 文字 |
| コンシューマー グループあたりの非エポック受信者の数 |エンティティ |- |5 |
| イベント データの最大リテンション期間 |エンティティ |- |1 ～ 7 日間 |
| 最大スループット ユニット |名前空間 |スループット ユニットの制限を超えると、データが調整され、**[ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception)** が生成されます。 [サポート リクエスト](/azure/azure-supportability/how-to-create-azure-support-request)を申請することによって、Standard レベルの多数のスループット ユニットをリクエストできます。 [追加スループット ユニット](../articles/event-hubs/event-hubs-auto-inflate.md)は、20 単位で購入できます。 |20 |
| 名前空間ごとの承認規則の数 |名前空間|上限を超えると承認規則の作成要求が拒否されます。|12 |
