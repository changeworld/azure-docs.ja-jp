---
title: Azure Maps の QPS レート制限
description: 1 秒あたりのクエリ数に関する Azure Maps の制限。
author: stevemunk
ms.author: v-munksteve
ms.date: 10/15/2021
ms.topic: quickstart
ms.service: azure-maps
services: azure-maps
manager: eriklind
ms.openlocfilehash: 0015eeccd2ea4ed12eb0435cac72db8e86f9119a
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846447"
---
# <a name="azure-maps-qps-rate-limits"></a>Azure Maps の QPS レート制限

Azure Maps には、実行できる要求の数に対して 1 日あたりの上限はありません。ただし、1 秒あたりのクエリ (QPS) の最大数には制限があります。

価格レベル別の各 Azure Maps サービスの QPS 使用制限を次に示します。

| Azure Maps サービス | QPS の制限: Gen 2 価格レベル | QPS の制限: Gen 1 S1 価格レベル | QPS の制限: Gen 1 S0 価格レベル |
|  ----------------- |  :--------------------------: | :------------------------------: | :------------------------: |
| Copyright サービス | 10 | 10 | 10 |
| Creator - エイリアス、TilesetDetails | 10 | 利用不可 | 利用不可 |
| Creator - 変換、データセット、地物状態、WFS | 50 | 利用不可 | 利用不可 |
| Data Service | 50 | 50 | 50 |
| Elevation Service | 50 | 50 | 50 |
| 位置情報サービス | 50 | 50 | 50 |
| Render サービス - 等高線タイル、DEM タイル、標高タイル、カスタマー タイル、トラフィック タイル、および静的マップ | 50 | 50 | 50 |
| Render サービス - 道路タイル | 500 | 500 | 50 |
| Render サービス - 衛星タイル | 250 | 250 | 50 |
| Render サービス - 気象タイル | 100 | 100 | 50 |
| ルーティング サービス - バッチ | 10 | 10 | 10 |
| ルーティング サービス - バッチ以外 | 50 | 50 | 50 |
| 検索サービス - バッチ | 10 | 10 | 10 |
| 検索サービス - バッチ以外 | 500 | 500 | 50 |
| 検索サービス - バッチ以外の逆引き | 250 | 250 | 50 |
| 空間サービス | 50 | 50 | 50 |
| タイムゾーン サービス | 50 | 50 | 50 |
| Traffic サービス | 50 | 50 | 50 |
| 天気サービス | 50 | 50 | 50 |

QPS の制限に達すると、HTTP 429 エラーが返されます。 Gen 2 または Gen 1 S1 の価格レベルを使用している場合は、[Azure portal](https://ms.portal.azure.com/) で Azure Maps マップ "*テクニカル*" サポート リクエストを作成して、必要に応じて特定の QPS 制限を引き上げすることができます。 Gen 1 S0 価格レベルの QPS 制限を引き上げることはできません。
