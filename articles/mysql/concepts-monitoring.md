---
title: Azure Database for MySQL での監視
description: この記事では、Azure Database for MySQL での監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: c3cba00077fd65239382d6fdd98e73a55f926b3b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/17/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Azure Database for MySQL での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for MySQL には、MySQL サーバーをサポートするリソースの動作への洞察を提供する各種のメトリックが用意されています。 

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
これらのメトリックは、Azure Database for MySQL に使用できます。

|メトリック|メトリックの表示名|単位|説明|
|---|---|---|---|---|
|cpu_percent|CPU 使用率|パーセント|使用されている CPU の割合|
|memory_percent|メモリの割合|パーセント|使用されているメモリの割合|
|io_consumption_percent|IO の割合|パーセント|使用されている IO の割合|
|storage_percent|ストレージの割合|パーセント|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|バイト|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、およびサーバー ログが含まれます。|
|storage_limit|ストレージの制限|バイト|このサーバーの最大のストレージ|
|active_connections|アクティブな接続の合計|カウント|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続の合計|カウント|サーバーへの失敗した接続の数|


## <a name="next-steps"></a>次の手順
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-on-metric.md)に関するページをご覧ください。
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
