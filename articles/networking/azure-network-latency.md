---
title: Azure ネットワーク ラウンドトリップ待ち時間統計 |Microsoft Docs
description: Azure リージョン間のラウンドトリップ待ち時間統計について説明します。
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586872"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure ネットワーク ラウンドトリップ待ち時間統計

Azure は、内部監視ツールを使用してネットワークのコア領域の待ち時間 (速度) を継続的に監視するほか、サードパーティの総合監視サービス [ThousandEyes](https://thousandeyes.com) によって収集された測定値も監視します。

## <a name="how-are-the-measurements-collected"></a>測定値の収集方法

待ち時間の測定値は、世界中の Azure クラウド リージョンでホストされている ThousandEyes エージェントから収集されます。エージェントはネットワーク プローブを 1 分間隔で相互に継続的に送信します。 月ごとの待ち時間の統計は、その月に収集されたサンプルを平均して算出されます。

## <a name="october-2019-latency-figures"></a>2019 年 10 月の待ち時間の数値

2019 年 10 月 31 日に終わる 31 日間の場合、集計されたリージョン内での月間の最小および最大のラウンドトリップ待ち時間は次のようになります。

- **北米**リージョン内のラウンドトリップは **5 ミリ秒**から **72 ミリ秒**。
- **ヨーロッパ** リージョン内のラウンドトリップは **3 ミリ秒**から **28 ミリ秒**。
- **アジア** リージョン内のラウンドトリップは **4 ミリ秒**から **134 ミリ秒**。

次に示すリージョン間の待ち時間の測定値は [ThousandEyes](https://thousandeyes.com) から得られたものです。 次の表の測定単位はミリ秒 (ms) です。

![Azure リージョン間の待ち時間統計](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>次の手順
- [Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)について確認します。