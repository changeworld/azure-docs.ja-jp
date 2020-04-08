---
title: 監視とパフォーマンスのチューニング
description: Azure SQL Database での監視とパフォーマンス チューニングの機能および手法の概要。
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.date: 03/10/2020
ms.openlocfilehash: 837d88665c1fdffe902c9c478e5d6dc65a2e402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/28/2020
ms.locfileid: "79232471"
---
# <a name="monitoring-and-performance-tuning-in-azure-sql-database"></a>Azure SQL Database の監視とパフォーマンス チューニング

Azure SQL Database 内のデータベースのパフォーマンスを監視するには、まず、特定のサービス レベルとパフォーマンス レベルの選択時に選択したデータベース パフォーマンスのレベルを基準にして、ワークロードで使用される CPU および IO リソースを監視します。 これを実現するために、Azure SQL Database では、Azure portal 内で表示できる、または次のいずれかの SQL 管理ツールを使用して表示できるリソース メトリックを出力します:[Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) または [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)。

単一データベースとプールされたデータベースの場合、Azure SQL Database には、パフォーマンスを向上させるために、インテリジェントなパフォーマンス チューニングの推奨設定と自動チューニング オプションを提供するデータベース アドバイザーが多数用意されています。 また、Query Performance Insight では、単一データベースとプールされたデータベースの CPU と IO の使用率が最も高いクエリについての詳細が表示されます。

Azure SQL Database では、データベースやソリューションのパフォーマンスのトラブルシューティングを行い、これらを最大化するのに役立つ、人工知能によって支えられる追加の監視およびチューニング機能が提供されます。 使用と分析 (具体的には [SQL Analytics](../azure-monitor/insights/azure-sql.md) を使用) のために、複数の宛先のいずれかに対してこれらの [Intelligent Insights](sql-database-intelligent-insights.md) およびその他の SQL Database リソース ログとメトリックの[ストリーミング エクスポート](sql-database-metrics-diag-logging.md)を構成することを選択できます。 Azure SQL Analytics は、1 つのビューですべての Azure SQL データベースのパフォーマンスを、複数のサブスクリプションにわたって大規模に監視するための先進のクラウド監視ソリューションです。 エクスポートできるログとメトリックの一覧については、[エクスポートの診断テレメトリ](sql-database-metrics-diag-logging.md#diagnostic-telemetry-for-export-for-azure-sql-database)に関するページをご覧ください

最後に、SQL には、[SQL Server クエリ ストア](https://docs.microsoft.com/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)および[動的管理ビュー (DMV)](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/system-dynamic-management-views) を備えた独自の監視機能と診断機能が用意されています。 さまざまなパフォーマンスの問題を監視するスクリプトについては、[DMV を使用した監視](sql-database-monitoring-with-dmvs.md)に関する記事をご覧ください。

## <a name="monitoring-and-tuning-capabilities-in-the-azure-portal"></a>Azure portal の監視およびチューニング機能

Azure portal では、Azure SQL Database により、すべてのデプロイの種類向けのリソース メトリックの監視が提供されます。 また、単一データベースとプールされたデータベースについては、データベース アドバイザーと Query Performance Insight によってクエリ チューニングの推奨設定とクエリ パフォーマンスの分析情報が提供されます。 最後に、Azure portal では、論理サーバーと、その単一およびプールされたデータベースに対して自動化を有効にすることができます。

### <a name="sql-database-resource-monitoring"></a>SQL Database のリソース監視

Azure portal 内の **[メトリック]** ビューで、次のリソース メトリックをすばやく監視できます。

- **DTU の使用状況**

  データベースまたはエラスティック プールが長時間にわたって 100% の DTU 使用率に達しているかどうかを確認します。 DTU 使用率が高い場合は、より多くの CPU または IO リソースがワークロードに必要になる可能性があることを示しています。 最適化が必要なクエリを示している場合もあります。
- **CPU 使用率**

  データベース、エラスティック プール、またはマネージド インスタンスが長時間にわたって 100% の CPU 使用率に達しているかどうかを確認します。 CPU 使用率が高い場合は、より多くの CPU または IO リソースがワークロードに必要になる可能性があることを示しています。 最適化が必要なクエリを示している場合もあります。
- **IO 使用率**

  データベース、エラスティック プール、またはマネージド インスタンスが、基盤となるストレージの IO 制限に達しているかどうかを確認します。 IO 使用率が高い場合は、より多くの CPU または IO リソースがワークロードに必要になる可能性があることを示しています。 最適化が必要なクエリを示している場合もあります。

  ![リソース メトリック](./media/sql-database-monitor-tune-overview/resource-metrics.png)

### <a name="database-advisors"></a>データベース アドバイザー
' Azure SQL Database には、単一データベースとプールされたデータベースのパフォーマンス チューニングに関する推奨設定を提供する[データベース アドバイザー](sql-database-advisor.md)が含まれています。 これらの推奨設定は、Azure portal と [PowerShell](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabaseadvisor) を使用して入手できます。 また、これらのチューニング推奨設定を SQL Database で自動的に実装できるように、[自動チューニング](sql-database-automatic-tuning.md)を有効にすることもできます。

### <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](sql-database-query-performance.md) では、単一データベースとプールされたデータベースに対して実行するクエリのうち、最も負荷が高いものと実行時間が長いもののパフォーマンスが Azure portal に示されます。

## <a name="generate-intelligent-assessments-of-performance-issues"></a>パフォーマンスの問題のインテリジェントな評価を生成する

Azure SQL Database [Intelligent Insights](sql-database-intelligent-insights.md) では、組み込まれているインテリジェンスを使って、人工知能によりデータベースの使用状況が継続的に監視され、パフォーマンス低下の原因となる破壊的なイベントが検出されます。 Intelligent Insights では、クエリ実行の待機時間、エラー、またはタイムアウトに基づいて、Azure SQL Database 内のデータベースのパフォーマンスの問題が自動的に検出されます。 検出されると、詳細な分析が実行され、[問題のインテリジェントな評価](sql-database-intelligent-insights-troubleshoot-performance.md)を含むリソース ログ (通称 SQLInsights) が生成されます。 このアセスメントは、データベース パフォーマンスの問題の根本原因分析と、可能な場合にはパフォーマンス向上に関する推奨事項で構成されます。

Intelligent Insights は、Azure の組み込みインテリジェンスの固有の機能であり、次の価値を提供します。

- プロアクティブな監視
- カスタマイズされたパフォーマンスの洞察
- データベースのパフォーマンス低下の早期検出
- 検出された問題の根本原因分析
- パフォーマンス向上に関する推奨事項
- 数十万のデータベースのスケールアウト機能
- DevOps リソースと総保有コストに対する良い影響

## <a name="enable-the-streaming-export-of-metrics-and-resource-logs"></a>メトリックとリソース ログのストリーミング エクスポートを有効にする

Intelligent Insights リソース ログを含む複数の宛先のいずれかに対する[診断テレメトリのストリーミング エクスポート](sql-database-metrics-diag-logging.md)を有効にし、構成できます。 この追加の診断テレメトリを使用してパフォーマンスの問題を特定し、解決するには、[SQL Analytics](../azure-monitor/insights/azure-sql.md) およびその他の機能を使用します。

診断設定を構成して、単一データベース、プールされたデータベース、エラスティック プール、マネージド インスタンス、インスタンス データベースのメトリックおよびリソース ログを次のいずれかの Azure リソースにストリーム配信できます。

### <a name="log-analytics-workspace-in-azure-monitor"></a>Azure Monitor の Log Analytics ワークスペース

[Azure Monitor の Log Analytics ワークスペース](../azure-monitor/platform/resource-logs-collect-workspace.md)にメトリックおよびリソース ログをストリーム配信できます。 ここでストリーム配信されたデータは、[SQL Analytics](../azure-monitor/insights/azure-sql.md) で使用できます。これは、パフォーマンス レポート、アラート、軽減策の推奨事項を含むデータベースのインテリジェントな監視機能を提供する、クラウドのみの監視ソリューションです。 Log Analytics ワークスペースにストリーム配信されたデータは、収集された他の監視データと組み合わせて分析できます。また、アラートや視覚化などの他の Azure Monitor 機能を利用することもできます。

### <a name="azure-event-hubs"></a>Azure Event Hubs

メトリックとリソース ログは、[Azure Event Hubs](../azure-monitor/platform/resource-logs-stream-event-hubs.md) にストリーム配信できます。 診断テレメトリをイベント ハブにストリーム配信して、次の機能を提供します。

- **サード パーティ製のロギングおよびテレメトリ システムにログをストリーム配信する**

  すべてのメトリックとリソース ログを 1 つのイベント ハブにストリーム配信して、ログ データをサード パーティの SIEM またはログ分析ツールにパイプします。
- **カスタムのテレメトリおよびログ プラットフォームを構築する**

  高い拡張性の公開サブスクライブを特長とするイベント ハブを使用することで、メトリックとリソース ログをカスタム テレメトリ プラットフォームに柔軟に取り込むことができます。 詳細については、「[Azure Event Hubs でのグローバル スケール テレメトリ プラットフォームの設計とサイズ変更](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/)」を参照してください。
- **データを Power BI にストリーム配信してサービスの正常性を表示する**

  Event Hubs、Stream Analytics、Power BI を使用して、診断データを Azure サービスのほぼリアルタイムの分析情報に転換します。 このソリューションの詳細については、「[Stream Analytics と Power BI:ストリーミング データのリアルタイム分析ダッシュボード](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-power-bi-dashboard)」を参照してください。

### <a name="azure-storage"></a>Azure Storage

メトリックとリソース ログを [Azure Storage](../azure-monitor/platform/resource-logs-collect-storage.md) にストリーム配信します。 Azure ストレージを使用して、前述の 2 つのストリーミング オプションの何分の 1 かのわずかなコストで、膨大な量の診断テレメトリをアーカイブできます。

## <a name="use-extended-events-in-the-sql-database-engine"></a>SQL データベース エンジンの拡張イベントを使用する

さらに、SQL の[拡張イベント](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)を使用して、高度な監視とトラブルシューティングを追加で行うことができます。 拡張イベントのアーキテクチャを使用すると、ユーザーは、パフォーマンスの問題のトラブルシューティングや特定に必要なデータを過不足なく収集できます。 SQL Database の拡張イベントの使用の詳細については、「[SQL Database の拡張イベント](sql-database-xevent-db-diff-from-svr.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- 単一データベースとプールされたデータベースのインテリジェント パフォーマンスに関する推奨事項の詳細については、「[データベース アドバイザーのパフォーマンスに関する推奨事項](sql-database-advisor.md)」を参照してください。
- パフォーマンスの問題の自動診断および根本原因分析を含むデータベース パフォーマンスの自動監視については、[Azure SQL Intelligent Insights](sql-database-intelligent-insights.md) に関するページを参照してください。
'''''''''