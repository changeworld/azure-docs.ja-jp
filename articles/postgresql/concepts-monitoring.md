---
title: "Azure Database for PostgreSQL での監視 | Microsoft Docs"
description: "この記事では、Azure Database for PostgreSQL での監視およびアラート生成のためのメトリック (CPU、制限、ストレージ、および接続の統計を含む) について説明します。"
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: d48159fbc5e52bf1916a744e3912ca7ceb0ab60f
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for PostgreSQL には、PostgreSQL サーバーをサポートするリソースの動作に関する洞察を提供する各種のメトリックが用意されています。 

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 

メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 

その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
Azure Database for PostgreSQL では、次のメトリックを使用できます。

|メトリック|メトリックの表示名|単位|Description|
|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|compute_limit|コンピューティング ユニットの制限|カウント|このサーバーのコンピューティング ユニットの最大数|
|compute_consumption_percent|コンピューティング ユニットの割合|Percent|サーバーの最大数のうち使用されているコンピューティング ユニットの割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、およびサーバー ログが含まれます。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ|
|active_connections|アクティブな接続の合計|カウント|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続の合計|カウント|サーバーへの失敗した接続の数|


> [!NOTE]
> コンピューティング ユニットは、メモリと CPU で構成されます。 コンピューティング ユニットの割合 (%) は max(memory%, cpu%) です。 どちらがコンピューティング ユニットの割合 (%) の変化に寄与しているかを正確に特定するには、メモリと CPU のグラフを調べます。 詳細については、[コンピューティング ユニット](concepts-compute-unit-and-storage.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次のステップ
- 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
