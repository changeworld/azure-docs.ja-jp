---
title: ログ データを収集する
titleSuffix: Azure Cognitive Search
description: 診断設定を有効にしてログ データを収集して分析した後、Kusto クエリ言語を使用して結果を調べます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 86e869bc08552ea11728c508486a4784eccf4042
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462364"
---
# <a name="collect-and-analyze-log-data-for-azure-cognitive-search"></a>Azure Cognitive Search 用のログ データを収集して分析する

診断ログまたは操作ログでは、Azure Cognitive Search の詳細な操作に関する分析情報が提供され、サービスとワークロードのプロセスの監視に役立ちます。 内部的には、ログがバックエンドに存在しているのは、ユーザーがサポート チケットを提出した場合に調査と分析が行われるのに十分な短い時間です。 ただし、操作データを自分で管理したい場合は、診断設定を構成して、ログ情報が収集される場所を指定する必要があります。

ログを設定することは、診断と操作履歴の保持に役立ちます。 ログを有効にした後は、クエリを実行したり、構造化分析のためにレポートを作成したりできます。

次の表では、データを収集して保持するためのオプションの一覧を示します。

| リソース | 使用目的 |
|----------|----------|
| [Log Analytics ワークスペースに送信する](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-resource-logs) | イベントとメトリックは Log Analytics ワークスペースに送信され、ポータルでそのクエリを実行して、詳細な情報を取得できます。 概要については、[Azure Monitor ログの使用](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata)に関するページを参照してください |
| [Blob Storage でアーカイブする](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-overview) | イベントとメトリックは BLOB コンテナーにアーカイブされて、JSON ファイルに格納されます。 ログは非常に細かい単位 (時間/分ごと) で収集でき、特定のインシデントの調査には便利ですが、自由な調査には役立ちません。 未加工のログ ファイルを表示するには JSON エディターを使用し、ログ データを集計および視覚化するには Power BI を使用します。|
| [イベント ハブへのストリーム](https://docs.microsoft.com/azure/event-hubs/) | イベントとメトリックは、Azure Event Hubs サービスにストリーム配信されます。 非常に大きなログに対する代替データ コレクション サービスとしては、これを選択します。 |

Azure Monitor ログと Blob Storage は、どちらも無料のサービスとして利用でき、Azure サブスクリプションの有効期間にわたって無料で試すことができます。 Application Insights は、アプリケーション データのサイズが一定の制限以下である限りは無料でサインアップして使用できます (詳しくは[価格のページ](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください)。

## <a name="prerequisites"></a>前提条件

Log Analytics または Azure Storage を使用している場合は、前もってリソースを作成できます。

+ [Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

+ [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)

## <a name="enable-data-collection"></a>データ収集を有効にする

診断設定では、ログに記録されたイベントとメトリックを収集する方法を指定します。

1. **[監視]** で **[診断設定]** を選択します。

   ![診断設定](./media/search-monitor-usage/diagnostic-settings.png "診断設定")

1. **[+ 診断設定を追加する]** を選択します

1. **[Log Analytics]** をオンにして、ワークスペースを選択し、 **[OperationLogs]** と **[AllMetrics]** を選択します。

   ![データ コレクションを構成する](./media/search-monitor-usage/configure-storage.png "データ収集を構成する")

1. 設定を保存します。

1. ログ記録を有効にしたら、検索サービスを使用してログとメトリックの生成を始めます。 ログに記録されたイベントとメトリックが使用できるようになるまでには時間がかかります。

Log Analytics の場合、データが使用可能になるまで数分かかり、その後は、Kusto クエリを実行してデータを取得できます。 詳細については、[クエリ要求の監視](search-monitor-logs.md)に関する記事をご覧ください。

Blob Storage の場合は、コンテナーが Blob Storage に表示されるまで 1 時間かかります。 1 時間ごと、コンテナーごとに、1 つの BLOB があります。 コンテナーは、ログ記録または測定するアクティビティが発生した場合にのみ作成されます。 データをストレージ アカウントにコピーすると、データは JSON 形式でフォーマットされ、次の 2 つのコンテナーに置かれます。

+ insights-logs-operationlogs: 検索トラフィックのログ用
+ insights-metrics-pt1m: メトリック用

## <a name="query-log-information"></a>ログ情報のクエリを実行する

診断ログでは、次の 2 つのテーブルに Azure Cognitive Search 用のログとメトリックが格納されています: **AzureDiagnostics** と **AzureMetrics**。

1. **[監視]** で **[ログ]** を選択します。

1. クエリ ウィンドウに「**AzureMetrics**」と入力します。 この単純なクエリを実行して、このテーブルに収集されているデータを把握します。 テーブル全体をスクロールして、メトリックと値を表示します。 上部に表示されているレコード数に注意してください。サービスでメトリックの収集がしばらく行われている場合は、時間間隔を調整して、管理しやすいデータ セットを取得できます。

   ![AzureMetrics テーブル](./media/search-monitor-usage/azuremetrics-table.png "AzureMetrics テーブル")

1. 次のクエリを入力して、表形式の結果セットを取得します。

   ```
   AzureMetrics
    | project MetricName, Total, Count, Maximum, Minimum, Average
   ```

1. **AzureDiagnostics** について前の手順を繰り返し、すべての列の情報を取得した後、より選択的なクエリを実行して、さらに興味深い情報を抽出します。

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search" 
   ```

   ![AzureDiagnostics テーブル](./media/search-monitor-usage/azurediagnostics-table.png "AzureDiagnostics テーブル")

## <a name="log-schema"></a>ログのスキーマ

Azure Cognitive Search のログ データが格納されるデータ構造は、次のスキーマに準拠しています。 

Blob Storage の場合、各 BLOB には、ログ オブジェクトの配列が含まれる、**レコード**と呼ばれるルート オブジェクトが 1 つあります。 各 BLOB には、同じ時間帯に行われたすべての操作に関するレコードが含まれます。

次の表では、診断ログに共通するフィールドの一部を示します。

| 名前 | Type | 例 | Notes |
| --- | --- | --- | --- |
| timeGenerated |DATETIME |"2018-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用している ResourceId |
| operationName |string |"Query.Search" |操作の名前 |
| operationVersion |string |"2019-05-06" |使用されている API バージョン |
| category |string |"OperationLogs" |定数 (constant) |
| resultType |string |"Success" |指定できる値成功または失敗 |
| resultSignature |INT |200 |HTTP の結果コード |
| durationMS |INT |50 |操作時間 (ミリ秒) |
| properties |object |次の表を参照 |操作固有データを含むオブジェクト |

### <a name="properties-schema"></a>プロパティのスキーマ

以下のプロパティは、Azure Cognitive Search に固有のものです。

| 名前 | Type | 例 | Notes |
| --- | --- | --- | --- |
| Description_s |string |"GET /indexes('content')/docs" |操作のエンドポイント |
| Documents_d |INT |42 |処理されたドキュメント数 |
| IndexName_s |string |"test-index" |操作に関連付けられているインデックスの名前 |
| Query_s |string |"?search=AzureSearch&$count=true&api-version=2019-05-06" |クエリ パラメーター |

## <a name="metrics-schema"></a>メトリックのスキーマ

メトリックはクエリ要求についてキャプチャされ、1 分間隔で測定されます。 各メトリックは、1 分あたりの最小値、最大値、および平均値を公開します。 詳細については、[クエリ要求の監視](search-monitor-queries.md)に関する記事をご覧ください。

| 名前 | Type | 例 | Notes |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |お使いのリソース ID |
| metricName |string |"Latency" |メトリックの名前 |
| time |DATETIME |"2018-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
| average |INT |64 |メトリックに応じた、メトリックの時間間隔での生のサンプルの平均値 (秒またはパーセント単位)。 |
| minimum |INT |37 |メトリックの時間間隔内の生のサンプルの最小値 (秒単位)。 |
| maximum |INT |78 |メトリックの時間間隔内の生のサンプルの最大値 (秒単位)。  |
| total |INT |258 |メトリックの時間間隔内の生のサンプルの合計値 (秒単位)。  |
| count |INT |4 |1 分間隔内でノードからログに出力されたメトリックの数。  |
| timegrain |string |"PT1M" |ISO 8601 でのメトリックの時間グレイン。 |

クエリはミリ秒単位で実行されるのが一般的であるため、秒として測定されるクエリのみが QPS などのメトリックに表示されます。

**Search Queries Per Second** メトリックの場合、最小値は、該当する 1 分間に登録された秒あたりの検索クエリ数のうち最小の値になります。 最大値も同様です。 平均値は、1 分間にわたって集計されます。 たとえば、1 分間のうち、1 秒は非常に負荷が高く、続く 58 秒は平均的な負荷で、最後の 1 秒はクエリが 1 つだけというパターンがあるとします。この場合、最初の 1 秒の値が SearchQueriesPerSecond の最大値、最後の １ 秒の値が最小値となります。

**Throttled Search Queries Percentage** の場合は、最小値、最大値、平均値、および合計値はすべて同じ値になります。これは、スロットルされた検索クエリの割合であり、1 分間の検索クエリの合計数に基づきます。

## <a name="view-raw-log-files"></a>生のログ ファイルを表示する

ログ ファイルのアーカイブには、Blob Storage が使用されます。 任意の JSON エディターを使用して、ログ ファイルを表示できます。 お持ちでない場合は、[Visual Studio Code](https://code.visualstudio.com/download) をお勧めします。

1. Azure portal でストレージ アカウントを開きます。 

2. 左側のナビゲーション ウィンドウで、 **[BLOB]** をクリックします。 **insights-logs-operationlogs** と **insights-metrics-pt1m** が表示されるはずです。 これらのコンテナーは、ログ データが Blob Storage にエクスポートされるときに、Azure Cognitive Search によって作成されます。

3. .json ファイルが表示されるまで、フォルダー階層を下に移動します。  コンテキスト メニューを使用して、ファイルをダウンロードします。

ファイルのダウンロードが済んだら、JSON エディターで開いて内容を表示します。

## <a name="next-steps"></a>次のステップ

まだ行っていない場合は、検索サービスの監視の基礎を確認し、すべての監視機能について理解してください。

> [!div class="nextstepaction"]
> [Azure Cognitive Search の操作とアクティビティを監視する](search-monitor-usage.md)