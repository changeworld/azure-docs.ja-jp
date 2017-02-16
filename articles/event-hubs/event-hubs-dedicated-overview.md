---
title: "Azure Event Hubs Dedicated の概要 | Microsoft Docs"
description: Microsoft Azure Event Hubs
services: event-hubs
author: banisadr
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: babanisa
translationtype: Human Translation
ms.sourcegitcommit: 6aebaeccc502d20c8a80297972b8f3b5e8e363c6
ms.openlocfilehash: 3cd3d3fa7a540f9608ac07172bb78ee719d6b0a6


---


# <a name="dedicated-event-hubs--an-overview"></a>Event Hubs Dedicated – 概要

Event Hubs Dedicated 容量は、最も厳しい要件を持つお客様にシングル テナント デプロイを提供します。 フルスケールの Azure Event Hubs では、十分な耐久性を備えたストレージと 1 秒未満の待機時間により、1 秒当たり 200 万件以上のイベントまたは 1 秒当たり最大 2 GB のテレメトリを受信できます。 また、同じシステム上でのリアルタイム処理とバッチ処理により、統合されたソリューションを実現できます。 このプランに含まれる Event Hubs Archive により、1 つのストリームでリアルタイムのパイプラインとバッチ ベースのパイプラインの両方をサポートできるので、ソリューションの複雑さが軽減されます。

次の表では、Event Hubs で提供されるサービス レベルを比較しています。 Standard および Basic のほとんどの機能が従量課金であるのに対し、Event Hubs Dedicated プランは月額固定料金となっています。 Dedicated レベルの機能は Standard プランと同じですが、要求の厳しいワークロードを実行するお客様向けにエンタープライズ スケールの容量で提供されます。

|  | Basic | Standard | 専用 |
| --- |:---:|:---:|:---:|
| イングレス イベント | 100 万イベントごとの課金 | 100 万イベントごとの課金 | あり |
| スループット ユニット (1 MB/秒の受信、2 MB/秒の送信) | 1 時間ごとの課金 | 1 時間ごとの課金 | あり |
| メッセージ サイズ | 256 KB | 256 KB | 1 MB |
| パブリッシャー ポリシー | 該当なし | ✓ | ✓ |     
| コンシューマー グループ | 1 - 既定 | 20 | 20 |
| メッセージ リプレイ | ✓ | ✓  | ✓ |
| 最大スループット ユニット | 20 | 20 (～ 100)  | 1 CU≈200 |
| 仲介型接続 | 100 (付属) | 1,000 (付属) | 100,000 (付属) |
| 追加の仲介型接続 | 該当なし | ✓ | ✓ |
| メッセージのリテンション期間 | 1 日分が含まれます | 1 日分が含まれます | 最大 7 日分が含まれます |
| Archive  <sup>プレビュー</sup> | 該当なし  | 1 時間ごとの課金 | あり |

## <a name="benefits-of-event-hubs-at-dedicated-capacity-include"></a>Dedicated 容量の Event Hubs の利点は次のとおりです。

* 他のテナントからのノイズがないシングル テナント ホスティング
* メッセージ サイズを 1 MB に拡大 (Standard および Basic では 256 KB)
* パフォーマンスを常に再現可能
* ニーズの急増に対応するための容量を保証
* 1 ～8 容量ユニット (CU) の範囲でスケーラブル - 1 秒あたり最大 200 万件のイングレス イベントを提供
  * 容量ユニット (CU) によって Dedicated Event Hubs のスケールを管理。CU ごとに約 200 スループット ユニット (TU) を提供可能
* メンテナンス不要 - 負荷分散、OS の更新、セキュリティ パッチ、パーティション分割は Microsoft が管理
* 月額固定料金

Dedicated Event Hubs では、Standard プランのスループットの制限もいくつか削除されます。 Basic および Standard レベルのスループット ユニット (TU) では、1 秒あたり 1000 件のイベントまたは TU あたり 1MBps の受信 (送信量の 2 倍) が提供されます。 Dedicated スケール プランには、イングレス イベントとエグレス イベントの数に制限はありません。 これらの制限は、購入した Dedicated Event Hubs の処理容量によってのみ決定されます。

このサービスは最大規模のテレメトリ ユーザーを対象としており、Enterprise Agreement で提供されます。

## <a name="how-to-onboard"></a>利用を開始する方法

Dedicated Event Hubs プラットフォームは、さまざまなサイズの容量ユニット (CU) で Enterprise Agreement を通じて一般に提供されます。 CU ごとに約 200 スループット ユニットが提供され、1 時間あたり 31 ドルで課金されます。 CU を追加または削除することによって、ひと月の間にニーズに合わせて容量をスケールアップまたはスケールダウンできます。 Dedicated プランは、お客様に適した柔軟なデプロイを実現するために、Event Hubs 製品チームからより実践的な利用方法が提供されるという点でほかにはないサービスとなっています。 


## <a name="next-steps"></a>次のステップ

[Dedicated Event Hubs の料金](https://azure.microsoft.com/en-us/pricing/details/event-hubs/)

Event Hubs Dedicated 容量の詳細については、Microsoft の営業担当者または Microsoft サポートにお問い合わせください。


<!--HONumber=Jan17_HO4-->


