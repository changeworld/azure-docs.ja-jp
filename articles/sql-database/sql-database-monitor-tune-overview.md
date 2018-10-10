---
title: 監視とパフォーマンスのチューニング - Azure SQL Database | Microsoft Docs
description: 評価と改善を通じて Azure SQL Database のパフォーマンスをチューニングするためのヒントを紹介します。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: v-daljep
ms.reviewer: carlrab
manager: craigg
ms.date: 07/16/2018
ms.openlocfilehash: c79285247950510791ede915fcf0e5373792044f
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165043"
---
# <a name="monitoring-and-performance-tuning"></a>監視とパフォーマンスのチューニング

Azure SQL Database は、自動的に管理される柔軟なデータ サービスです。このサービスでは、使用状況の監視、リソース (CPU、メモリ、I/O) の追加と削除、データベースのパフォーマンス向上に役立つ推奨事項の検索を簡単に行うことができるほか、データベースをワークロードに適合させてパフォーマンスを自動的に最適化することもできます。

## <a name="overview-of-monitoring-database-performance-in-azure-sql-database"></a>Azure SQL Database におけるデータベース パフォーマンスの監視の概要
Azure での SQL データベースのパフォーマンスの監視は、選択したデータベース パフォーマンスのレベルに対するリソース使用率を監視することから始めます。 [DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)または[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)では、監視することで、データベースに余分な容量があるかどうかや、リソースが上限に達したことで問題が発生しているかどうかを判断でき、さらに、データベースのコンピューティング サイズとサービス レベルを調整する必要があるかどうかを判断することもできます。 データベースの監視には、[Azure portal](https://portal.azure.com) で、グラフィカル ツールや SQL の[動的管理ビュー (DMV)](sql-database-monitoring-with-dmvs.md) を使用できます。

Azure SQL Database では、[パフォーマンス チューニングの推奨事項](sql-database-advisor.md)を確認することで、リソースを変更することなくクエリのパフォーマンスを改善および最適化する機会を見極めることができます。 インデックスの不足や、最適化が不完全なクエリは、データベース パフォーマンスが低下する一般的な原因です。 このようなチューニングの推奨事項を適用すると、ワークロードのパフォーマンスを向上させることができます。
識別されたすべての推奨事項を適用し、それによってデータベースのパフォーマンスが向上することを確認することで、Azure SQL Database で[クエリのパフォーマンスを自動的に最適化](sql-database-automatic-tuning.md)することもできます。 データベースのパフォーマンスの監視とトラブルシューティングには、次のオプションを使用できます。

- [Azure Portal](https://portal.azure.com) で、**[SQL データベース]** をクリックし、データベースを選択してから、監視グラフを使用して上限に近づいているリソースを見つけます。 DTU 消費は、既定で表示されます。 **[編集]** をクリックして、表示される時間の範囲と値を変更します。
- [Query Performance Insight](sql-database-query-performance.md) を使用して、最もリソースを消費しているクエリを特定します。
- [SQL Database Advisor](sql-database-advisor-portal.md) を使用して、インデックスの作成と削除、クエリのパラメーター化、およびスキーマの問題の修正に関する推奨事項を表示します。
- データベース パフォーマンスの自動監視には、[Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) を使用します。 パフォーマンスの問題が検出されたら、問題の詳細と根本原因分析 (RCA) が記載された診断ログが生成されます。 可能な場合は、パフォーマンス改善の推奨事項も提供されます。
- [自動チューニングを有効にします](sql-database-automatic-tuning-enable.md)。これにより、Azure SQL Database では、特定されたパフォーマンスの問題を自動的に修正できるようになります。
- [動的管理ビュー (DMV)](sql-database-monitoring-with-dmvs.md)、[拡張イベント (`XEvents`)(sql-database/sql-database-xevent-db-diff-from-svr.md)、および[クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)を使用して、リアルタイムでパフォーマンス パラメーターを取得することもできます。 これらのレポートまたはビューを使用して問題を特定した場合に Azure SQL Database のパフォーマンス向上に使用できる手法を探すには、[パフォーマンス ガイダンス](sql-database-performance-guidance.md)を参照してください。

## <a name="monitor-databases-using-the-azure-portal"></a>Azure ポータルを使用したデータベースの監視
[Azure Portal](https://portal.azure.com/) では、データベースを選択して **[監視]** グラフをクリックすると、単一のデータベースの使用率を監視することができます。 これにより、**[メトリック]** ウィンドウが表示されます。**[グラフの編集]** ボタンをクリックすると、内容を編集できます。 次のメトリックを追加します。

* CPU の割合
* DTU の割合
* データ IO の割合
* データベース サイズの割合

これらのメトリックを追加すると、**[監視]** グラフでこれらを引き続き確認しながら、**[メトリック]** ウインドウにさらに詳細な情報を表示することができます。 4 つのメトリックはいずれも、データベースの **DTU** を基準とする平均使用率を示しています。 サービス レベルの詳細については、[DTU ベースの購入モデル](sql-database-service-tiers-dtu.md)と[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)に関する記事を参照してください。  

![データベース パフォーマンスのサービス階層の監視](./media/sql-database-single-database-monitoring/sqldb_service_tier_monitoring.png)

また、パフォーマンス メトリックに対してアラートを構成することができます。 **[メトリック]** ウィンドウの **[アラートの追加]** ボタンをクリックします。 ウィザードの指示に従ってアラートを構成します。 メトリックが特定のしきい値を超えた場合、またはメトリックが特定のしきい値を下回った場合に警告するオプションがあります。

たとえば、データベースのワークロードが増加する見込みの場合、データベースのいずれかのパフォーマンス メトリックが 80% に達すると電子メールのアラートを送信するように構成できます。 このアラートは、1 つ上位のコンピューティング サイズに切り替えるタイミングを示す早期の警告として使用できます。

下位のコンピューティング サイズにダウングレードできるかどうかを判断するために、パフォーマンス メトリックを利用することもできます。 たとえば、Standard S2 データベースを使用していて、すべてのパフォーマンス メトリックは、どの時点でもデータベースの平均的な使用率が 10% を超えないとします。 この場合、データベースは Standard S1 で快適に動作します。 ただし、下位のコンピューティング サイズへの移行を決定する前に、急上昇や変動するワークロードに注意してください。

## <a name="improving-database-performance-with-more-resources"></a>リソースを増やしてデータベースのパフォーマンスを向上させる

最後に、データベースのパフォーマンス向上につながるすぐに実施可能な方法がない場合は、Azure SQL Database で使用できるリソースの量を変更することができます。 単一データベースの[DTU サービス レベル](sql-database-service-tiers-dtu.md)を変更するか、エラスティック プールの eDTU を増やすことにより、いつでもリソース割り当てを増やすことができます。 あるいは、[仮想コアベースの購入モデル](sql-database-service-tiers-vcore.md)を使用した場合は、サービス レベルを変更することも、データベースへの割り当てリソースを増やすこともできます。 
1. 単一データベースの場合は、[サービス レベルの変更](sql-database-service-tiers-dtu.md)や[コンピューティング リソース](sql-database-service-tiers-vcore.md)の変更をオンデマンドで行うことで、データベースのパフォーマンスを改善できます。
2. 複数のデータベースの場合は、リソースを自動的にスケーリングするための [エラスティック プール](sql-database-elastic-pool-guidance.md) の使用を検討してください。

## <a name="tune-and-refactor-application-or-database-code"></a>アプリケーションまたはデータベース コードをチューニングおよびリファクタリングする

データベースの使用、インデックスの変更、計画の強制的な適用、データベースをワークロードに手動で適合させるためのヒントの使用をより最適に行うために、アプリケーション コードを変更することができます。 手動によるチューニングとコードの書き直しに関するガイダンスとヒントについては、[パフォーマンス ガイダンスのトピック](sql-database-performance-guidance.md)を参照してください。


## <a name="next-steps"></a>次の手順

- Azure SQL Database で自動チューニングを有効にし、自動チューニング機能でワークロードを完全に管理できるようにするには、「[Enable automatic tuning (自動チューニングの有効化)](sql-database-automatic-tuning-enable.md)」を参照してください。
- 手動チューニングを使用するには、[Azure Portal のチューニングに関する推奨事項](sql-database-advisor-portal.md)を確認したうえで、クエリのパフォーマンスを向上させる推奨事項を手動で適用してください。
- [Azure SQL Database のサービス レベル](sql-database-performance-guidance.md)を変更して、データベースで使用可能なリソースを変更します。
