---
title: Azure SQL Data Warehouse の管理と監視 - クエリのアクティビティ、リソースの使用状況 | Microsoft Docs
description: Azure SQL Data Warehouse の管理と監視に利用できる機能について説明します。 データ ウェアハウスのクエリ アクティビティとリソース使用状況を把握するには、Azure portal と動的管理ビュー (DMV) を使用します。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 08/26/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: c783045d242725ee19dfe7e0baee13625d986312
ms.sourcegitcommit: 2b2129fa6413230cf35ac18ff386d40d1e8d0677
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/30/2018
ms.locfileid: "43246496"
---
# <a name="monitoring-resource-utilization-and-query-activity-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのリソース使用状況とクエリ アクティビティを監視する
Azure portal 内にある Azure SQL Data Warehouse のリッチな監視エクスペリエンスでは、データ ウェアハウスのワークロードに関する分析情報が表示されます。 データ ウェアハウスを監視するときの推奨されるツールである Azure portal では、構成可能なリテンション期間、アラート、推奨事項、およびメトリックとログのカスタマイズ可能なグラフとダッシュボードが提供されます。 ポータルでは、Operations Management Suite (OMS)/Log Analytics や Azure Monitor などの他の Azure 監視サービスと統合して、お使いのデータ ウェアハウスだけでなく、統合された監視エクスペリエンスに対する Azure 分析プラットフォーム全体も含む、総合的な監視エクスペリエンスを提供することもできます。 このドキュメントでは、SQL Data Warehouse で分析プラットフォームの最適化と管理に使用できる監視機能について説明します。 

## <a name="resource-utilization"></a>リソース使用状況 
以下のメトリックは Azure portal で利用できます。

| メトリックの名前                           | 説明     | 集計の種類 |
| --------------------------------------- | ---------------- | --------------------------------------- |
| CPU の割合                          | データ ウェアハウスの全ノードでの CPU 使用率 | 最大値      |
| データ IO の割合                      | データ ウェアハウスの全ノードでの IO 使用率 | 最大値   |
| 成功した接続                  | データへの成功した接続の数 | 合計            |
| 失敗した接続                      | データ ウェアハウスへの失敗した接続の数 | 合計            |
| ファイアウォールによってブロックされる                     | ブロックされたデータ ウェアハウスへのログインの数 | 合計            |
| DWU 上限                              | データ ウェアハウスのサービス レベル目標 | 最大値   |
| DWU の割合                          | CPU の割合とデータ IO の割合の最大値 | 最大値   |
| 使用済み DWU                                | DWU の制限 * DWU の割合 | 最大値   |
| キャッシュ ヒットの割合 | (キャッシュ ヒット数/キャッシュ ミス数) * 100: キャッシュ ヒット数はローカル SSD キャッシュでのすべての列ストア セグメント ヒット数の合計、キャッシュ ミス数はローカル SSD キャッシュでの列ストア セグメント ミス数を全ノードについて合計した値 | 最大値 |
| 使用されたキャッシュの割合 | (使用されたキャッシュ/キャッシュ容量) * 100: 使用されたキャッシュは全ノードのローカル SSD キャッシュでの全バイト数の合計、キャッシュ容量は全ノードのローカル SSD キャッシュのストレージ容量の合計 | 最大値 |

## <a name="query-activity"></a>クエリ アクティビティ
T-SQL を使用して SQL Data Warehouse を監視するときのプログラム エクスペリエンスでは、動的管理ビュー (DMV) のセットがサービスによって提供されます。 これらのビューは、アクティブのトラブルシューティングを行うときと、ワークロードでパフォーマンスのボトルネックを特定するときに役に立ちます。

SQL Data Warehouse で提供される DMV の一覧を表示するのには、[こちらのドキュメント](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-reference-tsql-system-views#sql-data-warehouse-dynamic-management-views-dmvs)をご覧ください。 

## <a name="metrics-and-diagnostics-logging"></a>メトリックと診断のロギング
メトリックとログはどちらも、[Operations Management Suite](https://azure.microsoft.com/resources/videos/operations-management-suite-oms-overview/) (OMS) (具体的には [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) コンポーネント) にエクスポートでき、[ログ検索](https://docs.microsoft.com/azure/log-analytics/log-analytics-tutorial-viewdata)を使用してプログラムでアクセスできます。


> [!NOTE]
> 2018 年 8 月現在、ログは SQL Data Warehouse 用の実装が行われています

## <a name="next-steps"></a>次の手順
次のハウツー ガイドでは、データ ウェアハウスを監視および管理するときの一般的なシナリオとユース ケースについて説明されています。

- [DMV を利用してデータ ウェアハウスのワークロードを監視する](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-manage-monitor)

