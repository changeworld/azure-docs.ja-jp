---
title: Azure Database for PostgreSQL での監視
description: この記事では、Azure Database for PostgreSQL での監視およびアラート生成のためのメトリック (CPU、ストレージ、および接続の統計を含む) について説明します。
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: d0a57fe6d7b1040c32f6d67e2bf0259176c72099
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/28/2018
ms.locfileid: "29687610"
---
# <a name="monitoring-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL での監視
サーバーに関する監視データは、ワークロードをトラブルシューティングしたり最適化したりするのに役立ちます。 Azure Database for PostgreSQL には、PostgreSQL サーバーをサポートするリソースの動作に関する洞察を提供する各種のメトリックが用意されています。 

## <a name="metrics"></a>メトリック
すべての Azure メトリックは 1 分間隔で、各メトリックの 30 日間の履歴が保持されます。 メトリックにアラートを構成できます。 詳細な手順については、[アラートの設定方法](howto-alert-on-metric.md)に関する記事をご覧ください。 その他のタスクとして、自動化されたアクションの設定、高度な分析の実行、履歴のアーカイブなどがあります。 詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。

### <a name="list-of-metrics"></a>メトリックの一覧
Azure Database for PostgreSQL では、次のメトリックを使用できます。

|メトリック|メトリックの表示名|単位|[説明]|
|---|---|---|---|---|
|cpu_percent|CPU 使用率|Percent|使用されている CPU の割合|
|memory_percent|メモリの割合|Percent|使用されているメモリの割合|
|io_consumption_percent|IO の割合|Percent|使用されている IO の割合|
|storage_percent|ストレージの割合|Percent|サーバーの最大数のうち使用されているストレージの割合|
|storage_used|使用済みストレージ|Bytes|使用されているストレージの量。 サービスで使用されるストレージには、データベース ファイル、トランザクション ログ、およびサーバー ログが含まれます。|
|storage_limit|ストレージの制限|Bytes|このサーバーの最大のストレージ|
|active_connections|アクティブな接続の合計|Count|サーバーへのアクティブな接続の数|
|connections_failed|失敗した接続の合計|Count|サーバーへの失敗した接続の数|


## <a name="next-steps"></a>次の手順
- メトリックに対するアラートの作成のガイダンスについては、[アラートを設定する方法](howto-alert-on-metric.md)に関するページをご覧ください。
- Azure Portal、REST API、または CLI を使用してメトリックへのアクセスおよびメトリックのエクスポートを行う方法の詳細については、[Azure のメトリックの概要](../monitoring-and-diagnostics/monitoring-overview-metrics.md)に関する記事をご覧ください。
