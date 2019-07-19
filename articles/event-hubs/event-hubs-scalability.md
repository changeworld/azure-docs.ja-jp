---
title: スケーラビリティ - Azure Event Hubs | Microsoft Docs
description: Azure Event Hubs のスケーリングする方法について説明します。
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 06/18/2019
ms.author: shvija
ms.openlocfilehash: 3eb20013a6b3afaddce10f2e4652add0edf22a9a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276528"
---
# <a name="scaling-with-event-hubs"></a>Event Hubs によるスケーリング

Event Hubs によるスケーリングに影響する 2 つの要素があります。
*   スループット ユニット
*   パーティション

## <a name="throughput-units"></a>スループット ユニット

Event Hubs のスループット容量は、"*スループット ユニット*" によって制御されます。 スループット ユニットとは、購入済みの容量ユニットのことです。 1 つのスループットでは次が可能です。

* イングレス: 1 秒あたり最大で 1 MB または 1,000 イベント (どちらか先に到達した方)
* エグレス: 1 秒あたり最大で 2 MB または 4,096 イベント

購入済みのスループット ユニットの容量を超えると、イングレスが調整され、[ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) が返されます。 エグレスではスロットル例外は発生しませんが、購入済みのスループット ユニットの容量に制限されます。 発行率の例外を受信するか、より高いエグレスが予想される場合は、名前空間に対して購入したスループット ユニットの数を確認してください。 スループット ユニットは、[Azure Portal](https://portal.azure.com) の名前空間の **[スケール]** ブレードで管理できます。 [Event Hubs API](event-hubs-api-overview.md) を使用して、プログラムでスループット ユニットを管理することもできます。

スループット ユニットは事前に購入し、1 時間ごとに課金されます。 スループット ユニットを購入すると、少なくとも 1 時間の料金が課金されます。 Event Hubs の名前空間に対して最大 20 のスループット ユニットを購入でき、その名前空間内のすべてのイベント ハブで共有されます。

Event Hubs の**自動インフレ**機能は、使用量のニーズに合わせてスループット単位の数を増やすことで、自動的にスケールアップします。 スループット単位を増やすことで、以下の状況で必要になる調整シナリオを防ぐことができます。

- データの受信レートが、設定されたスループット単位を超えている。
- データの送信要求レートが、設定されたスループット単位を超えている。

負荷が最小しきい値を超えていて、ServerBusy エラーなどで失敗した要求がない場合、Event Hubs サービスでスループットが増えます。 

自動インフレ機能の詳細については、[スループット単位の自動的なスケーリング](event-hubs-auto-inflate.md) に関する記事を参照してください。

## <a name="partitions"></a>パーティション

パーティションにより、ダウン ストリーム処理用にスケールできます。 Event Hubs がパーティションによって提供するパーティション分割されたコンシューマー モデルのため、イベントの処理中に同時にスケール アウトできます。 Event Hubs には、最大 32 個のパーティションを指定できます。

最適なスケールを実現するために、スループット ユニットとパーティションのバランスを 1:1 に保つことをお勧めします。 単一のパーティションには、最大で 1 つのスループット ユニットのイングレスとエグレスが保証されます。 パーティションでより高いスループットを実現することもできますが、パフォーマンスは保証されません。 このため、イベント ハブのパーティションの数は、スループット ユニットの数以上にすることを強くお勧めします。

合計スループットを必要に応じて計画するとして、必要なスループット ユニットの数と最小数のパーティションはわかりますが、パーティションはいくつ必要でしょうか。 実現したいダウンストリーム並列処理だけでなく、将来のスループットのニーズに基づいて、パーティションの数を選択します。 Event Hubs 内にあるパーティションの数に対して料金はかかりません。

Event Hubs の価格の詳細については、「[Event Hubs の価格](https://azure.microsoft.com/pricing/details/event-hubs/)」を参照してください。

## <a name="next-steps"></a>次の手順
Event Hubs の詳細については、次のリンク先を参照してください:

- [スループット単位の自動スケール](event-hubs-auto-inflate.md)
- [Event Hubs サービスの概要](event-hubs-what-is-event-hubs.md)
