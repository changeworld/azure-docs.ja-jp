---
title: Azure ネットワーク ラウンドトリップ待ち時間統計 |Microsoft Docs
description: Azure リージョン間のラウンドトリップ待ち時間統計について説明します。
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 01/08/2020
ms.author: mnayak
ms.openlocfilehash: 91b528cc6900a3ec91ff7189f58f941226b8acd5
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/09/2020
ms.locfileid: "75779724"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure ネットワーク ラウンドトリップ待ち時間統計

Azure は、内部監視ツールを使用してネットワークのコア領域の待ち時間 (速度) を継続的に監視するほか、サードパーティの総合監視サービス [ThousandEyes](https://thousandeyes.com) によって収集された測定値も監視します。

## <a name="how-are-the-measurements-collected"></a>測定値の収集方法

待ち時間の測定値は、世界中の Azure クラウド リージョンでホストされている ThousandEyes エージェントから収集されます。エージェントはネットワーク プローブを 1 分間隔で相互に継続的に送信します。 月ごとの待ち時間の統計は、その月に収集されたサンプルを平均して算出されます。

## <a name="december-2019-latency-figures"></a>2019 年 12 月の待ち時間の数値

過去 30 日間の Azure リージョン間の毎月の平均ラウンドトリップ時間 (2019 年 12 月 31 日に終了) を以下に示します。 次に示す測定値は [ThousandEyes](https://thousandeyes.com) から得られたものです。

[![Azure リージョン間の待ち時間統計](media/azure-network-latency/december.jpg)](media/azure-network-latency/december.jpg#lightbox)

## <a name="next-steps"></a>次のステップ

[Azure リージョン](https://azure.microsoft.com/global-infrastructure/regions/)について確認します。
