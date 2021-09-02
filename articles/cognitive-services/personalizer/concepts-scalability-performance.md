---
title: スケーラビリティとパフォーマンス - Personalizer
titleSuffix: Azure Cognitive Services
description: 高パフォーマンスでトラフィックの多い Web サイトやアプリケーションには、スケーラビリティとパフォーマンスのために Personalizer で考慮すべき 2 つの主な要素があります。それは、待機時間とトレーニングのスループットです。
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 711d1f69485fb7f4c9256ac4a2edbe351ae4619c
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831406"
---
# <a name="scalability-and-performance"></a>スケーラビリティとパフォーマンス

高パフォーマンスでトラフィックの多い Web サイトやアプリケーションには、スケーラビリティとパフォーマンスのために Personalizer で考慮すべき 2 つの主な要素があります。

* Rank API 呼び出しを行うときに低待機時間を維持する
* トレーニングのスループットがイベント入力に追いついていることを確認する

パーソナル化からは高速に順位が返され、呼び出し時間のほとんどを REST API を介した通信が占めます。 Azure では、要求に迅速に対応するために、機能が自動スケーリングされます。

##  <a name="low-latency-scenarios"></a>低待機時間シナリオ

一部のアプリケーションでは、順位を返すときに低待機時間を必要とします。 低待機時間は次のために必要です。

* 優先度付けされたコンテンツが表示されるまでに、ユーザーが長時間待機しないようにする。
* 極端なトラフィックが発生しているサーバーによって、十分ではないコンピューティング時間とネットワーク接続が妨害されないように防ぐ。


## <a name="scalability-and-training-throughput"></a>スケーラビリティとトレーニングのスループット

Rank および Reward API の後に Personalizer から非同期的に送信されるメッセージに基づいて再トレーニングされるモデルを更新することで、Personalizer は機能します。 これらのメッセージは、アプリケーション用の Azure EventHub を使用して送信されます。

 ほとんどのアプリケーションは、Personalizer の上限の参加およびトレーニングのスループットに達する可能性はほぼありません。 この上限に達してもアプリケーションは遅くなりませんが、Event Hub キューがクリーンアップされるよりも早く内部的にいっぱいになる可能性があります。

## <a name="how-to-estimate-your-throughput-requirements"></a>スループット要件を推定する方法

* コンテキストとアクションの JSON ドキュメントの長さを足し合わせて、優先度付け イベントあたりの平均バイト数を推定します。
* この推定平均バイト数で 20 MB/秒を割ります。

たとえば、平均的なペイロードに 500 個の機能があり、それぞれが推定 20 文字の場合、各イベントは約 10 KB です。 このような推定の場合、20,000,000 / 10,000 = 2,000 イベント/秒、つまり約 1 億 7,300 万イベント/日です。 

このような制限に達している場合は、Microsoft のサポート チームに連絡してアーキテクチャのアドバイスを受けてください。

## <a name="next-steps"></a>次のステップ

[Personalizer の作成と構成](how-to-settings.md)