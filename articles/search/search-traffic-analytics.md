<properties 
	pageTitle="Azure Search の検索トラフィックの分析 |Microsoft Azure" 
	description="Azure Search の検索トラフィックの分析 (Microsoft Azure 上のクラウド ホスト検索サービス) を有効にして、ユーザーとデータに関する洞察のロックを解除します。" 
	services="search" 
	documentationCenter="" 
	authors="bernitorres" 
	manager="pablocas" 
	editor=""
/>

<tags 
	ms.service="search" 
	ms.devlang="multiple" 
	ms.workload="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/26/2016" 
	ms.author="betorres"
/>


# 検索トラフィックの分析の有効化と使用

検索トラフィックの分析は、使用している検索サービスを表示し、ユーザーとその動作に関する洞察のロックを解除できる Azure Search の機能です。この機能を有効にすると、検索サービスのデータは選択しているストレージ アカウントにコピーされます。このデータには、検索サービスのログと集計された運用メトリックが含まれます。データがコピーされると、任意の方法で利用状況データを処理および操作することができます。


## 検索トラフィックの分析を有効にする方法

### 1\.ポータルの使用
[Azure ポータル](http://portal.azure.com)で Azure Search サービスを開きます。[設定] には、検索トラフィックの分析オプションが表示されます。

![][1]

このオプションを選択して、新しいブレードが開きます。[状態] を **[オン]** に変更し、データがコピーされる Azure Storage アカウントを選択して、コピーするデータ (ログ、メトリック、または両方) を選択します。ログとメトリックをコピーすることをお勧めします。

![][2]


> [AZURE.IMPORTANT] ストレージ アカウントは、検索サービスと同じリージョンおよび同じサブスクリプション内にある必要があります。
> 
> このストレージ アカウントには標準料金が適用されます。

### 2\.PowerShell の使用

また、次の PowerShell コマンドレットを実行して、この機能を有効にすることもできます。

```PowerShell
Login-AzureRmAccount
Set-AzureRmDiagnosticSetting -ResourceId <SearchService ResourceId> StorageAccountId <StorageAccount ResourceId> -Enabled $true
```

-   **SearchService ResourceId**: ```
/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Search/searchServices/<searchServiceName>
```

 
-  **StorageAccount ResourceId**: [設定]、[プロパティ]、[ResourceId] の順に開いたポータル内に表示されます```
New: /subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>
OR
Classic: /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.ClassicStorage/storageAccounts/<storageAccountName>
```

----------

有効にすると、5 ～ 10 分以内にデータのストレージ アカウントへの送信が開始されます。Blob Storage に 2 つの新しいコンテナーが表示されます。

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


## データの説明

データは、JSON 形式で Azure Storage BLOB に格納されます。

1 時間ごと、コンテナーごとに、1 つの BLOB があります。
  
例のパス: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### Logs (ログ)

ログ BLOB には、検索サービスのトラフィック ログが含まれています。

各 BLOB には、ログ オブジェクトの配列を含む、**レコード** と呼ばれるルート オブジェクトが 1 つあります。

####ログのスキーマ

名前 |型 |例 |メモ 
------|-----|----|-----
time |datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ
resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用している ResourceId
operationName |string |"Query.Search" |操作の名前
operationVersion |string |"2015-02-28"|使用されている API バージョン
カテゴリ |string |"OperationLogs" |定数 
resultType |string |"Success" |使用可能な値: Success または Failure 
resultSignature |int |200 |HTTP の結果コード 
durationMS |int |50 |操作時間 (ミリ秒) 
プロパティ |オブジェクト |以下を参照 |操作固有データを含むオブジェクト

####プロパティのスキーマ

|名前 |型 |例 |メモ|
|------|-----|----|-----|
|説明|string |"GET /indexes('content')/docs" |操作のエンドポイント |
|クエリ |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |クエリ パラメーター |
|ドキュメント |int |42 |処理されたドキュメント数|
|IndexName |string |"testindex"|操作に関連付けられているインデックスの名前 |

### メトリック

メトリック BLOB には、検索サービスの集計値が含まれます。各ファイルには、メトリック オブジェクトの配列を含む、**レコード** と呼ばれるルート オブジェクトが 1 つあります。

使用可能なメトリック:

- 待機時間

####メトリックのスキーマ

|名前 |型 |例 |メモ|
|------|-----|----|-----|
|resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |使用しているリソース ID |
|metricName |string |"Latency" |メトリックの名前 |
|time|datetime |"2015-12-07T00:00:43.6872559Z" |操作のタイムスタンプ |
|average |int |64|メトリックの時間間隔内の生のサンプルの平均値 |
|minimum |int |37 |メトリックの時間間隔内の生のサンプルの最小値 |
|maximum |int |78 |メトリックの時間間隔内の生のサンプルの最大値 |
|total |int |258 |メトリックの時間間隔内の生のサンプルの合計値 |
|count |int |4 |メトリックの生成に使用される生のサンプル数 |
|timegrain |string |"PT1M" |ISO 8601 でのメトリックの時間グレイン|

## データの分析

データは使用しているストレージ アカウントにあり、自分の環境に最も適した方法で、このデータを検索することをお勧めします。

最初に、[Power BI](https://powerbi.microsoft.com) を使用して、データを検索および視覚化することをお勧めします。Azure Storage アカウントに容易に接続し、データの分析を迅速に開始できます。

#### Power BI Online

[Power BI コンテンツ パック](https://app.powerbi.com/getdata/services/azure-search): データが自動的に表示され、検索サービスに関する情報を視覚化できる Power BI ダッシュボードと Power BI レポートのセットを作成します。[コンテンツ パックのヘルプ ページ](https://powerbi.microsoft.com/ja-JP/documentation/powerbi-content-pack-azure-search/)を参照してください。

![][3]

#### Power BI Desktop

[Power BI Desktop](https://powerbi.microsoft.com/ja-JP/desktop): データを調査し、データの視覚化を作成します。以下のように、役立つ基本的なクエリが用意されています。

1. 新しい PowerBI Desktop レポートを開きます
2. [データの取得]、[詳細] の順に選択します

	![][4]

3. Microsoft Azure Blob Storage を選択して接続します

	![][5]

4. 使用しているストレージ アカウントの名前とアカウント キーを入力します
5. "insight-logs-operationlogs" と "insights-metrics-pt1m" を選択して、[編集] をクリックします
6. クエリ エディターが開き、左側で "insight-logs-operationlogs" が選択されていることを確認します。ここで、[表示]、[詳細エディター] の順に選択して、詳細エディターを開きます。

	![][6]

7. 最初の 2 つの行を保持し、残りの部分を次のクエリに置き換えます。

	>     #"insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
	>     #"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
	>     #"Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
	>     #"Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
	>     #"Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
	>     #"Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
	>     #"Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
	>     #"Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
	>     #"Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
	>     #"Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
	>     #"Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
	>     #"Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
	>     #"Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
	>     #"Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
	>     in
	>     #"Changed Type2"

8. [完了] をクリックします

9. ここで、クエリの左側にある "insights-metrics-pt1m" を選択し、再度 [詳細エディター] を開きます。最初の 2 つの行を保持し、残りの部分を次のクエリに置き換えます。

	>     #"insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
	>     #"Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
	>     #"Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
    	#"Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
	>     #"Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
	>     #"Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
	>     #"Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
	>     #"Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
	>     #"Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
	>     #"Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
	>     #"Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
	>     #"Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
	>         Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
	>     #"Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
	>     #"Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
	>     in
    	#"Inserted Date"

10. [完了] をクリックし、[ホーム] タブで [閉じて適用] を選択します。

11. これで過去 30 日間のデータを使用する準備ができました。今すぐ[視覚エフェクト](https://powerbi.microsoft.com/ja-JP/documentation/powerbi-desktop-report-view/)を作成しましょう。

## 次のステップ

検索構文とクエリ パラメーターについて詳細に学習します。詳細については、「[ドキュメントの検索 (Azure Search Service REST API)](https://msdn.microsoft.com/library/azure/dn798927.aspx)」を参照してください。

レポートの作成についての詳細。「[Power BI Desktop の概要](https://powerbi.microsoft.com/ja-JP/documentation/powerbi-desktop-getting-started/)」を参照してください。

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/Dashboard.png
[4]: ./media/search-traffic-analytics/GetData.png
[5]: ./media/search-traffic-analytics/BlobStorage.png
[6]: ./media/search-traffic-analytics/QueryEditor.png

<!---HONumber=AcomDC_0204_2016-->