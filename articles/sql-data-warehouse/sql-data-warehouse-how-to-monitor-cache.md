---
title: Gen2 キャッシュの最適化
description: Azure portal を使用して Gen2 キャッシュを監視する方法について説明します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.subservice: performance
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 924705b7ce1d324583077797714491bdf3fc5cc9
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76721219"
---
# <a name="how-to-monitor-the-gen2-cache"></a>Gen2 キャッシュを監視する方法
Gen2 ストレージ アーキテクチャでは、Gen2 データ ウェアハウス用に設計された NVMe ベースの SSD に搭載されているキャッシュ内で、最も頻繁に照会される列ストア セグメントを自動的に階層化します。 キャッシュに常駐しているセグメントをクエリが取得すると、より優れたパフォーマンスが実現します。 この記事では、ワークロードが Gen2 キャッシュを最適に活用しているかどうかを判断することで、パフォーマンスが遅いクエリを監視し、トラブルシューティングする方法について説明します。  
## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal を使用したトラブルシューティング
Azure Monitor を使用すれば、Gen2 キャッシュ メトリックを表示して、クエリのパフォーマンスをトラブルシューティングできます。 まず Azure portal に移動し、[監視] をクリックします。

![Azure Monitor](./media/sql-data-warehouse-cache-portal/cache_0.png)

メトリック ボタンを選択し、データ ウェアハウスの **[サブスクリプション]** 、 **[リソース** **グループ]** 、 **[リソースの種類]** 、 **[リソース名]** を記入します。

Gen2 キャッシュをトラブルシューティングするための主要なメトリックは、 **[キャッシュ ヒット率]** と **[キャッシュ使用率]** です。 次の 2 つのメトリックを表示するように、Azure メトリック グラフを構成します。

![キャッシュ メトリック](./media/sql-data-warehouse-cache-portal/cache_1.png)


## <a name="cache-hit-and-used-percentage"></a>キャッシュのヒット率と使用率
以下の表は、キャッシュ メトリックの値に基づくシナリオを示しています。

|                                | **高いキャッシュ ヒット率** | **低いキャッシュ ヒット率** |
| :----------------------------: | :---------------------------: | :--------------------------: |
| **高いキャッシュ使用率** |          シナリオ 1           |          シナリオ 2          |
| **低いキャッシュ使用率**  |          シナリオ 3           |          シナリオ 4          |

**シナリオ 1:** キャッシュを最適に使用しています。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](sql-data-warehouse-manage-monitor.md)してください。

**シナリオ 2:** 現在稼働中のデータ セットはキャッシュに収まりきらないため、物理的な読み取りのためにキャッシュ ヒット率が低くなります。 パフォーマンス レベルの拡張を検討し、ワークロードを再実行してキャッシュを設定してください。

**シナリオ 3:** キャッシュとは無関係な原因により、クエリが遅く実行されている可能性があります。 クエリを遅くしている可能性があるその他の領域を[トラブルシューティング](sql-data-warehouse-manage-monitor.md)してください。 また、[インスタンスを縮小し](sql-data-warehouse-manage-monitor.md)、キャッシュ サイズを削減してコストを節約することも検討してください。 

**シナリオ 4:** クエリが遅かったのは、コールド キャッシュが存在していたことが原因だった可能性があります。 現在稼働中のデータ セットがキャッシュされるように、クエリを再実行することを検討してください。 

> [!IMPORTANT]
> キャッシュヒット率またはキャッシュ使用率がワークロードの再実行後にも更新されない場合、稼働中のセットは既にメモリに常駐している可能性があります。 クラスター化された列ストア テーブルのみがキャッシュされます。

## <a name="next-steps"></a>次のステップ
一般的なクエリのパフォーマンス チューニングの詳細については、「[クエリの実行の監視](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md#monitor-query-execution)」を参照してください。
