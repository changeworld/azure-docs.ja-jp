---
title: Azure Synapse Analytics での履歴クエリのストレージと分析
description: 履歴クエリ分析は、データ エンジニアの重要なニーズの 1 つです。 Azure Synapse Analytics では、クエリ履歴とパフォーマンスを分析する 4 つの主な方法がサポートされています。 これには、クエリ ストア、DMV、Azure Log Analytics、Azure Data Explorer が含まれます。
author: mariyaali
ms.author: mariyaali
ms.reviewer: wiassaf
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/28/2021
ms.custom: template-concept
ms.openlocfilehash: 2e018b87caee86616a12d817d8c628eff93c7a3d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2021
ms.locfileid: "132720152"
---
# <a name="historical-query-storage-and-analysis-in-azure-synapse-analytics"></a>Azure Synapse Analytics での履歴クエリのストレージと分析

履歴クエリ分析は、データ エンジニアの重要なニーズの 1 つです。 Azure Synapse Analytics では、クエリ履歴とパフォーマンスを分析する 4 つの主な方法がサポートされています。 これには、クエリ ストア、DMV、Azure Log Analytics、Azure Data Explorer が含まれます。 

この記事では、これらの各オプションをニーズに合わせて使用する方法について説明します。 クエリ履歴の分析に関するユース ケースと、それぞれに最適な方法を確認します。

| **顧客ニーズ** |  **クエリ ストア** |  **DMV**    | **Azure Log Analytics** | **Azure Data Explorer** |
|------------- | --- | ----- | ------------- |-------------------|
|**すぐに利用できるソリューション** | 有効化が必要 | :heavy_check_mark: | 追加サービスが必要 |    追加サービスが必要|
|**長い分析期間** | 30 日 |    最大 10000 行の履歴     | カスタマイズ可能 | カスタマイズ可能|
|**重要なメトリックの可用性** |    制限あり    | :heavy_check_mark: |    制限あり    | カスタマイズ可能|
|**分析に SQL を使用** | :heavy_check_mark: | :heavy_check_mark:| KQL が必要 | SQL サポートは制限付き|
|||||

## <a name="query-store"></a>クエリ ストア

クエリ ストア機能を使用すると、クエリ プランの選択やパフォーマンスに関する分析情報を得られます。 これにより、クエリ プランの変更によって生じるパフォーマンスの違いがすばやくわかるようになり、パフォーマンス上のトラブルシューティングを簡略化できます。 

クエリ ストアは、新しい Azure Synapse Analytics データベースでは既定で有効になっていません。 クエリ ストアを有効にするには、次の T-SQL コマンドを実行します。

```sql
ALTER DATABASE <database_name>
SET QUERY_STORE = ON;
```

次に例を示します。

```sql
ALTER DATABASE [SQLPOOL1]
SET QUERY_STORE = ON;
```

パフォーマンスの監査とトラブルシューティング関連のタスクを実行するには、最後に実行されたクエリ、実行回数、実行時間が最長のクエリ、最大の物理 I/O 読み取り数を持つクエリを検索します。 クエリのサンプルについては、「[クエリのストアを使用した、パフォーマンスの監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store#performance)」を参照してください。

長所:
* ストレージ クエリ データには 30 日のしきい値。
* データは、クエリを実行するのと同じツールで使用できます。

既知の制限事項:
* DMV の使用と比較した場合、Azure Synapse のクエリ ストアでは分析のシナリオが制限されます。

## <a name="dmvs"></a>DMV

動的管理ビュー (DMV) は、クエリの待機時間、実行プラン、メモリなどに関する情報を収集する場合に非常に便利です。後で追跡するために、目的のクエリにラベルを付けることを強くお勧めします。 次に例を示します。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query');
```

Azure Synapse SQL でのクエリのラベル付けの詳細については、「[Synapse SQL でクエリ ラベルを使用する](develop-label.md)」を参照してください。

DMV を使用した Azure Synapse Analytics ワークロードの監視に関する詳細については、「[DMV を使用して専用 SQL プールのワークロードを監視する](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)」を参照してください。 Azure Synapse Analytics に固有のカタログ ビューに関するドキュメントについては、[Azure Synapse Analytics のカタログ ビュー](/sql/relational-databases/system-catalog-views/sql-data-warehouse-and-parallel-data-warehouse-catalog-views)に関するページを参照してください。

長所:
* データは、同じクエリ ツールで使用できます。
* DMV には、分析のためのさまざまなオプションが用意されています。

既知の制限事項:
* DMV は、10,000 行の履歴エントリに制限されています。 
* プールが一時停止または再開されると、ビューがリセットされます。

## <a name="log-analytics"></a>Log Analytics
Log Analytics ワークスペースは、Azure portal で簡単に作成できます。 Synapse を Log Analytics と接続する方法の詳細については、「[ワークロードを監視する - Azure portal](../sql-data-warehouse/sql-data-warehouse-monitor-workload-portal.md)」を参照してください。

Azure Data Explorer と同様に、Log Analytics では Kusto クエリ言語 (KQL) を使用します。 Kusto 構文の詳細については、「[Kusto クエリの概要](/azure/data-explorer/kusto/query/)」を参照してください。 

構成可能な保持期間と共に、Log Analytics でクエリを実行するために特に対象とするワークスペースを選択します。 Log Analytics を使用すると、データの保存、クエリの実行と保存を柔軟に行うことができます。

長所:
* Azure Log Analytics には、カスタマイズ可能なログ保持ポリシーがあります

既知の制限事項:
* KQL の使用が学習曲線に追加されます。
* 制限されたビューはそのまますぐに記録できます。

## <a name="azure-data-explorer-adx"></a>Azure Data Explorer (ADX)

Azure Data Explorer (ADX) は、最先端のデータ探索サービスです。 このサービスは、Azure Synapse Analytics からの履歴クエリを分析するために使用できます。 ログをコピーして Azure Data Factory (ADX) に保存するように ADF パイプラインを設定するには、「[Azure Data Explorer との間でデータをコピーする](../../data-factory/connector-azure-data-explorer.md)」を参照してください。 ADX では、パフォーマンスに優れた Kusto クエリを実行してログを分析できます。 たとえば ADF を使用して DMV 出力のクエリを実行して ADX に読み込むために、ここで他の戦略を組み合わせることができます。
  
長所:
* ADX には、カスタマイズ可能なログ保持ポリシーが用意されています。
* 大量のデータに対するパフォーマンスに優れたクエリ実行 (特に文字列検索を含むクエリ)。

既知の制限事項:
* KQL の使用が学習曲線に追加されます。

## <a name="next-steps"></a>次の手順

 - [Azure Data Explorer](/azure/data-explorer/)
 - [Azure Data Factory](../../data-factory/index.yml)
 - [Azure Monitor の Log Analytics](../../azure-monitor/logs/log-analytics-overview.md)
 - [Azure Synapse SQL アーキテクチャ](overview-architecture.md)
 - [Azure Synapse Analytics の使用を開始する](../get-started.md)