---
title: Azure Database for MariaDB での監視
description: この記事では、Azure Database for MariaDB での監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3c25798be8af26c1f5e5c1178395cd1688bb132
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382062"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for MariaDB には、サーバーの動作への洞察を提供する各種のメトリックが用意されています。

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

詳細な手順については、[アラートの設定方法](howto-alert-metric.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MariaDB に使用できます。

|メトリック|メトリックの表示名|単位|説明|
|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、サーバー ログが含まれることがあります。|
|serverlog_storage_percent|サーバー ログ ストレージの割合|Percent|サーバーの最大サーバー ログ ストレージのうち、使用されているサーバー ログ ストレージの割合。|
|serverlog_storage_usage|サーバー ログ ストレージの使用量|Bytes|使用されているサーバー ログ ストレージの量。|
|serverlog_storage_limit|サーバー ログ ストレージの上限|Bytes|このサーバーの最大サーバー ログ ストレージ。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ|
|active_connections|アクティブな接続|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続|Count|サーバーへの失敗した接続の数|
|network_bytes_egress|Network Out|Bytes|アクティブな接続全体のネットワーク送信。|
|network_bytes_ingress|Network In|Bytes|アクティブな接続全体のネットワーク受信。|

## <a name="server-logs"></a>サーバー ログ

サーバーで低速クエリ ログを有効にできます。 これらのログは、Azure Monitor ログ、Event Hubs、およびストレージ アカウントでの Azure 診断ログを通じて入手することもできます。 ログ記録の詳細については、 [サーバー ログ](concepts-server-logs.md)に関するページをご覧ください。

## <a name="query-store"></a>クエリ ストア

[クエリ ストア](concepts-query-store.md)は、クエリ ランタイム統計や待機イベントなど、一定期間のクエリ パフォーマンスを追跡記録します。 この機能は、**mysql** スキーマにクエリ ランタイムのパフォーマンス情報を保持します。 さまざまな構成ノブを介してデータのコレクションとストレージを制御できます。

## <a name="query-performance-insight"></a>Query Performance Insight

[Query Performance Insight](concepts-query-performance-insight.md) はクエリ ストアと連動し、データを視覚化します。視覚化したデータには Azure portal からアクセスできます。 これらのグラフにより、パフォーマンスに影響を与える主要なクエリを特定できます。 Query Performance Insight には、Azure Database for MariaDB サーバーのポータル ページの「**インテリジェント パフォーマンス**」セクションからアクセスできます。

## <a name="performance-recommendations"></a>パフォーマンスに関する推奨事項

[パフォーマンスの推奨事項](concepts-performance-recommendations.md)機能によって、ワークロード パフォーマンス改善の機会が特定されます。 パフォーマンスの推奨事項によって、ワークロードのパフォーマンスを改善する可能性がある新しいインデックスを作成するための推奨事項が提供されます。 推奨インデックスを作成するために、この機能は、クエリ ストアから報告されたスキーマ、ワークロードなどのさまざまなデータベースの特性を考慮します。 顧客は、パフォーマンスに関する推奨事項を実装した後、パフォーマンスをテストし、変更の影響を評価する必要があります。

## <a name="service-health"></a>サービス正常性
[Azure サービス正常性](../service-health/overview.md)は、サブスクリプション内のすべてのサービス正常性通知のビューを提供します。 使用している Azure サービスやリージョンに影響を与える可能性のある問題または変更が発生した場合は、希望する通信チャネル経由で通知を受けるようにサービス正常性アラートを設定できます。

**計画メンテナンス**のイベントの種類を使用して、Azure Database for MariaDB の予定されているメンテナンス イベントを表示できます。 **サービス正常性アラート**を作成する方法については、「[サービス通知のアクティビティ ログ アラートを作成する](../service-health/alerts-activity-log-service-notifications.md)」の記事を参照してください。

> [!IMPORTANT]
> 計画メンテナンスの通知は、米国東部および英国南部向けのプレビューでのみ使用できます。

## <a name="next-steps"></a>次の手順

- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
  - メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-metric.md)に関するページをご覧ください。
