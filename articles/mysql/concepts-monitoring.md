---
title: "Azure Database for MySQL での監視 | Microsoft Docs"
description: "この記事では、Azure Database for MySQL での監視およびアラート生成のためのメトリック (CPU、制限、ストレージ、および接続の統計を含む) について説明します。"
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 9af447d54faa8ee96e4b79beb274b437eea57626
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Azure Database for MySQL での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for MySQL には、MySQL サーバーをサポートするリソースの動作への洞察を提供する各種のメトリックが用意されています。 

## <a name="metrics"></a>メトリック
Azure メトリックはすべて 1 分間の頻度を持ち、各メトリックが 30 日間の履歴を提供します。 

メトリックにアラートを構成できます。 手順を追ったガイダンスについては、「[How to set up alerts (アラートを設定する方法)](howto-alert-on-metric.md)」を参照してください。 

その他のタスクには、自動化されたアクションの設定、高度な分析の実行、および履歴のアーカイブが含まれます。 詳細については、「[Azure Metrics Overview (Azure メトリックの概要)](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」を参照してください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MySQL に使用できます。

|メトリック|メトリックの表示名|単位|Description|
|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合 (%)。|
|compute_limit|コンピューティング ユニットの制限|カウント|このサーバーのコンピューティング ユニットの最大数|
|compute_consumption_percent|コンピューティング ユニットの割合|Percent|サーバーの最大数のうち使用されているコンピューティング ユニットの割合 (%)。|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合 (%)。|
|io_consumption_percent|IO の割合|Percent|使用されている I/O の割合 (%)。|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合 (%)。|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、およびサーバー ログが含まれます。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ。|
|active_connections|アクティブな接続の合計|カウント|サーバーへのアクティブな接続の数。|
|connections_failed|失敗した接続の合計|カウント|サーバーへの失敗した接続の数。|


> [!NOTE]
> コンピューティング ユニットは、メモリと CPU で構成されます。 コンピューティング ユニットの割合 (%) は max(memory%, cpu%) です。 どちらがコンピューティング ユニットの割合 (%) の変化に寄与しているかを正確に特定するには、メモリと CPU のグラフを調べます。 詳細については、[コンピューティング ユニット](concepts-compute-unit-and-storage.md)を参照してください。

## <a name="next-steps"></a>次のステップ
- 手順を追ったガイダンスについては、「[How to set up alerts (アラートを設定する方法)](howto-alert-on-metric.md)」を参照してください。 
- Azure Portal、REST API、または CLI を使用してメトリックにアクセスしたりエクスポートしたりする方法の詳細については、「[Azure Metrics Overview (Azure メトリックの概要)](../monitoring-and-diagnostics/monitoring-overview-metrics.md)」を参照してください。
