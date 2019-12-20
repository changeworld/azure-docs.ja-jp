---
title: Azure ネットワーク ラウンドトリップ待ち時間統計 |Microsoft Docs
description: Azure リージョン間のラウンドトリップ待ち時間統計について説明します。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/04/2019
ms.author: mnayak
ms.openlocfilehash: 3947df81b67d5aefc1b628b6ddaf8275152a4cd3
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893089"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure ネットワーク ラウンドトリップ待ち時間統計

Azure は、内部監視ツールを使用してネットワークのコア領域の待ち時間 (速度) を継続的に監視するほか、サードパーティの総合監視サービス [ThousandEyes](https://thousandeyes.com) によって収集された測定値も監視します。

## <a name="how-are-the-measurements-collected"></a>測定値の収集方法

待ち時間の測定値は、世界中の Azure クラウド リージョンでホストされている ThousandEyes エージェントから収集されます。エージェントはネットワーク プローブを 1 分間隔で相互に継続的に送信します。 月ごとの待ち時間の統計は、その月に収集されたサンプルを平均して算出されます。

## <a name="november-2019-latency-figures"></a>2019 年 11 月の待ち時間の数値

**11 月の更新プログラム:** 3 つのリージョンを追加しました。

* ノルウェー東部
* ノルウェー西部
* オーストラリア

過去 30 日間の Azure リージョン間の毎月の平均ラウンドトリップ時間 (2019 年11月30日に終了) を以下に示します。 次に示す測定値は [ThousandEyes](https://thousandeyes.com) から得られたものです。

![Azure リージョン間の待ち時間統計](media/azure-network-latency/latency-nov-2019.png)

## <a name="next-steps"></a>次の手順

[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)について確認します。
