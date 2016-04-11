<properties 
	pageTitle="Application Insights のクエリのサンプル - Analytics" 
	description="Application Insights のクエリのサンプル - Analytics、Application Insights の強力な検索ツール。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/21/2016" 
	ms.author="awills"/>

# Application Insights の Analytics サンプル

[Analytics](app-analytics.md) を使用すると、[Application Insights](app-insights-overview.md) によってアプリから収集されたテレメトリに対して強力なクエリを実行できます。ここでは、Analytics のクエリ言語について説明します。作業を開始する場合は、[言語のツアー](app-analytics-tour.md)を参照することをお勧めします。


[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

## クエリのベスト プラクティス 

クエリをより高速にするために実行できる操作とできない操作がいくつかあります。

実行できる操作:

-	最初に時間フィルターを使用する。Application Insights Analytics は、時間フィルターを利用するために高度に最適化されています。
-	クエリの開始時 (時間フィルターの直後) にほとんどのデータを削除すると思われるフィルターを配置する。
-	クエリの開始時 ('extend' の使用を開始する前) にほとんどのフィルターが表示されることを確認する。 
-	フル トークンを検索するときに、'contains' ではなく 'has' キーワードを優先する。'has' は、部分文字列を検索する必要がないため、より効率的です。
-	'*' (列全体のフル テキスト検索) を使用するのではなく、特定の列を調べる。
-	結合の両側から必要な列に対してのみ、join - project を使用する (これにより、コンピューター間でプルされるペイロードが減少します)。

実行できない操作:

-	末尾に 'limit (少数)' または 'count' を指定せずに新しいクエリを試す。不明なデータに対してバインドされていないクエリを実行すると、クライアントに数 GB の結果が返され、応答速度が低下し、クラスターがビジー状態になる可能性があります。
-	1B 以上のレコードに対して変換 (JSON や文字列など) を適用していることに気付いた場合は、クエリをリシェイプし、データの変換量を減らしてください。





<a name="activities"></a>
## 開始および停止イベントからのセッションの取得

イベントのログがあり、一部のイベントで拡張アクティビティまたはセッションの開始または終了がマークされているとします。

|名前|City|SessionId|Timestamp|
|---|---|---|---|
|開始|ロンドン|2817330|2015-12-09T10:12:02.32|
|Game|ロンドン|2817330|2015-12-09T10:12:52.45|
|開始|Manchester|4267667|2015-12-09T10:14:02.23|
|Stop|ロンドン|2817330|2015-12-09T10:23:43.18|
|キャンセル|Manchester|4267667|2015-12-09T10:27:26.29|
|Stop|Manchester|4267667|2015-12-09T10:28:31.72|

すべてのイベントに SessionId があります。したがって、問題は同じ ID を持つ開始および停止イベントのマッチングです。

```AIQL
let Events = MyLogTable | where ... ;

Events
| where Name == "Start"
| project Name, City, SessionId, StartTime=timestamp
| join (Events 
        | where Name="Stop"
        | project StopTime=timestamp, SessionId) 
    on activityId
| project City, SessionId, StartTime, StopTime, Duration = StopTime - StartTime
```

[`let`](app-analytics-syntax.md#let-statements) を使用して、結合に進む前に可能な限り減らすテーブルのプロジェクションを指定します。[`Project`](app-analytics-queries.md#project-operator) は、開始および停止の両方の時間を結果に表示できるように、タイムスタンプの名前を変更するために使用されます。結果に表示する他の列を選択することもできます。[`join`](app-analytics-queries.md#join-operator) は、同じアクティビティに対する開始および停止エントリのマッチングを行い、アクティビティごとに行を作成します。最後に、`project` はもう一度列を追加して、アクティビティ期間を表示します。


|City|SessionId|StartTime|StopTime|時間|
|---|---|---|---|---|
|ロンドン|2817330|2015-12-09T10:12:02.32|2015-12-09T10:23:43.18|00:11:40.46|
|Manchester|4267667|2015-12-09T10:14:02.23|2015-12-09T10:28:31.72|00:14:29.49|

### セッション ID なしのセッションの取得

ここでは、不便なことに、開始および停止イベントにマッチングで使用できるセッション ID がないと仮定します。ただし、セッションが行われたクライアントの IP アドレスはあります。各クライアントのアドレスでは一度に 1 つのセッションしか行われないと仮定した場合、同じ IP アドレスから各開始イベントと次の停止イベントのマッチングを行うことができます。

```AIQL
Events 
| where Name == "Start" 
| project City, ClientIp, StartTime = timestamp
| join  kind=inner
    (Events
    | where Name == "Stop" 
    | project StopTime = timestamp, ClientIp)
    on ClientIp
| extend duration = StopTime - StartTime 
    // Remove matches with earlier stops:
| where  duration > 0  
    // Pick out the earliest stop for each start and client:
| summarize argmin(duration, *) by bin(StartTime,1s), ClientIp
```

結合では、同じクライアントの IP アドレスから各開始時刻とすべての停止時刻のマッチングを行います。したがって、まず、前の停止時刻と一致した時刻を削除します。

次に、開始時刻と IP でグループ化してから、セッションごとのグループを取得します。StartTime パラメーターには `bin` 関数を指定する必要があります。そうしないと、Analytics は自動的に 1 時間単位のビンを使用して、一部の開始時刻と間違った停止時刻のマッチングを行います。

`argmin` は各グループの最短期間の行を選択し、`*` パラメーターは他のすべての列をパススルーしますが、各列名に "min\_" というプレフィックスを付けます。


![](./media/app-analytics-samples/040.png)

その後、コードをいくつか追加し、便利なサイズのビン単位で期間をカウントすることができます。棒グラフの方が若干見やすいため、`1s` 単位で期間を数値に変換します。


      // Count the frequency of each duration:
    | summarize count() by duration=bin(min_duration/1s, 10) 
      // Cut off the long tail:
    | where duration < 300
      // Prepare for display in a bar chart:
    | sort by duration asc 


![](./media/app-analytics-samples/050.png)


### 実例

```AIQL

Logs  
| filter ActivityId == "ActivityId with Blablabla" 
| summarize max(Timestamp), min(Timestamp)  
| extend Duration = max_Timestamp - min_Timestamp 
 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  	 
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedMaps > 0 
| summarize sum(TotalMapsSeconds) by UnitOfWorkId  
| extend JobMapsSeconds = sum_TotalMapsSeconds * 1 
| project UnitOfWorkId, JobMapsSeconds 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)   
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalReducesSeconds = ReducesSeconds / TotalLaunchedReducers 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2'  
| filter TotalLaunchedReducers > 0 
| summarize sum(TotalReducesSeconds) by UnitOfWorkId  
| extend JobReducesSeconds = sum_TotalReducesSeconds * 1 
| project UnitOfWorkId, JobReducesSeconds ) 
on UnitOfWorkId 
| join ( 
wabitrace  
| filter Timestamp >= datetime(2015-01-12 11:00:00Z)  
| filter Timestamp < datetime(2015-01-12 13:00:00Z)  
| filter EventText like "NotifyHadoopApplicationJobPerformanceCounters"  
| extend Tenant = extract("tenantName=([^,]+),", 1, EventText) 
| extend Environment = extract("environmentName=([^,]+),", 1, EventText)  
| extend JobName = extract("jobName=([^,]+),", 1, EventText)  
| extend StepName = extract("stepName=([^,]+),", 1, EventText)  
| extend UnitOfWorkId = extract("unitOfWorkId=([^,]+),", 1, EventText)  
| extend LaunchTime = extract("launchTime=([^,]+),", 1, EventText, typeof(datetime))  
| extend FinishTime = extract("finishTime=([^,]+),", 1, EventText, typeof(datetime)) 
| extend TotalLaunchedMaps = extract("totalLaunchedMaps=([^,]+),", 1, EventText, typeof(real))  
| extend TotalLaunchedReducers = extract("totalLaunchedReducers=([^,]+),", 1, EventText, typeof(real)) 
| extend MapsSeconds = extract("mapsMilliseconds=([^,]+),", 1, EventText, typeof(real)) / 1000 
| extend ReducesSeconds = extract("reducesMilliseconds=([^,]+)", 1, EventText, typeof(real)) / 1000 
| extend TotalMapsSeconds = MapsSeconds  / TotalLaunchedMaps  
| extend TotalReducesSeconds = (ReducesSeconds / TotalLaunchedReducers / ReducesSeconds) * ReducesSeconds  
| extend CalculatedDuration = (TotalMapsSeconds + TotalReducesSeconds) * time(1s) 
| filter Tenant == 'DevDiv' and Environment == 'RollupDev2') 
on UnitOfWorkId 
| extend MapsFactor = TotalMapsSeconds / JobMapsSeconds 
| extend ReducesFactor = TotalReducesSeconds / JobReducesSeconds 
| extend CurrentLoad = 1536 + (768 * TotalLaunchedMaps) + (1536 * TotalLaunchedMaps) 
| extend NormalizedLoad = 1536 + (768 * TotalLaunchedMaps * MapsFactor) + (1536 * TotalLaunchedMaps * ReducesFactor) 
| summarize sum(CurrentLoad), sum(NormalizedLoad) by  JobName  
| extend SaveFactor = sum_NormalizedLoad / sum_CurrentLoad 
```

<a name="concurrent-activities"><a/>
## 同時セッションの時系列グラフ

開始および終了時刻を含むアクティビティのテーブルがあるとします。ここでは、一定時間における同時実行数を示す時系列グラフを使用します。

以下に入力例を示します。これを `X` とします。

|SessionId | StartTime | StopTime |
|---|---|---|
| a | 10:01:03 | 10:10:08 |
| b | 10:01:29 | 10:03:10 |
| c | 10:03:02 | 10:05:20 |

グラフを 1 分単位のビン分割にしたいため、そのようなグラフを作成する必要があります。間隔を 1m にすれば、実行中のアクティビティごとにカウントできます。

中間結果を以下に示します。

     X | extend samples = range(bin(StartTime, 1m), Stop, 1m)

`range` は指定された間隔で値の配列を生成します。

|SessionId | StartTime | StopTime | サンプル|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | [10:01:00,10:02:00,...10:06:00]|
| b | 10:02:29 | 10:03:45 | [10:02:00,10:03:00]|
| c | 10:03:12 | 10:04:30 | [10:03:00,10:04:00]|

ただし、ここでは配列は保持せずに、次のように[展開](app-analytics-queries.md#mvexpand-operator)します。

    X | mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)


|SessionId | StartTime | StopTime | サンプル|
|---|---|---|---|
| a | 10:03:33 | 10:06:31 | 10:01:00|
| a | 10:03:33 | 10:06:31 | 10:02:00|
| b | 10:02:29 | 10:03:45 | 10:02:00|
| a | 10:03:33 | 10:06:31 | 10:03:00|
| b | 10:02:29 | 10:03:45 | 10:03:00|
| c | 10:03:12 | 10:04:30 | 10:03:00|
| a | 10:03:33 | 10:06:31 | 10:04:00|
| c | 10:03:12 | 10:04:30 | 10:04:00|
|...||||

これで、アクティビティごとの発生回数をカウントして、サンプル時間でグループ化できます。

```
X
| mvexpand samples = range(bin(StartTime, 1m), StopTime , 1m)
| summarize count(SessionId) by bin(todatetime(samples),1m)
```

* `mvexpand` では動的型の列が生成されるため、todatetime() が必要です。
* 数値や日付の場合、間隔を指定しないと集計では常に既定の間隔で bin 関数が適用されるため、bin() が必要になります。 


| count\_SessionId | サンプル|
|---|---|
| 1 | 10:01:00|
| 2 | 10:02:00|
| 3 | 10:03:00|
| 2 | 10:04:00|
| 1 | 10:05:00|

これは、棒グラフまたは時間グラフとして表示できます。


## 結合の種類

join 演算子の正確な種類は、kind キーワードを使用して指定されます。現時点では、Analytics は 6 種類の join 演算子をサポートします。つまり、左側が重複除去される内部結合 (既定)、標準内部結合、左外部結合、右外部結合、全外部結合、および左反結合の 6 つです。
 
ここでは (種類を指定せずに) 既定の結合で、以下の 2 つのサンプル テーブルを使用して結合操作について説明します。
 
テーブル X


|キー |Value1 
|---|---
|a |1 
|b |2 
|b |3 
|c |4 

テーブル Y

|キー |Value2 
|---|---
|b |10 
|c |20 
|c |30 
|d |40 

 
既定の結合では、結合キーの左側が重複除去されてから内部結合が行われます (重複除去では最初のレコードは維持されます)。以下のステートメントを指定します。

    X | join Y on Key 

この場合、結合の有効な左側は (重複除去後のテーブル X) は次のようになります。

|キー |Value1 
|---|---
|a |1 
|b |2 
|c |4 

結合の結果は次のようになります。

|キー |Value1 |キー |Value2 
|---|---|---|---
|b |2 |b |10 
|c |4 |c |20 
|c |4 |c |30 

(出力にキーの 'a' と 'd' が表示されていないことに注意してください。これは、左側と右側の両方に一致するキーがないためです)。
 
(歴史的に言えば、これが Analytics の初期バージョンでサポートされていた結合の最初の実装になります。これは、同じ関連付け ID で (それぞれがいくつかのフィルター条件に一致した) 2 つのイベントを関連付け、関係のあるトレース レコードの複数の発生数を無視して検索対象となる現象のすべての発生数を戻す一般的なログ/トレース分析シナリオで便利です。)
 
### 内部結合 (kind=inner) 

これは、SQL の世界では周知の標準的な内部結合です。左側のレコードの結合キーが右側のレコードと同じである場合は、常に出力レコードが生成されます。
 
    X | join kind=inner Y on Key 

結果は次のようになります。

|キー |Value1 |キー |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 

右側の (b,10) は 2 回、つまり、左側の (b,2) と (b,3) の両方と結合されています。また、左側の (c,4) も 2 回、つまり、右側の (c,20) と (c,30) の両方と結合されていることに注目してください。

### 左外部結合 (kind=leftouter) 

テーブル X と Y の左外部結合の結果には、常に左側のテーブル (X) のレコードがすべて含まれます。結合条件で、一致するレコードが右側のテーブル (Y) で検出されない場合でも同様です。
 
たとえば、以下のように指定したとします。

    X | join kind=leftouter Y on Key 

結果:

|キー |Value1 |キー |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
 
### 右外部結合 (kind=rightouter) 

左外部結合と似ていますが、テーブルの処理は逆になります。
 
たとえば、以下のように指定したとします。

    X | join kind=rightouter Y on Key 

結果:


|キー |Value1 |キー |Value2 
|---|---|---|---
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### 完全外部結合 (kind=fullouter) 

概念的には、完全外部結合は、左と右の両方の外部結合を適用した場合の効果を組み合わせたものです。結合したテーブルのレコードが一致しない場合、結果セットには、一致する行がないテーブルのすべての列の NULL 値が含まれます。一致するレコードがある場合は、結果セットに単一行が生成されます (両方のテーブルからデータが取り込まれたフィールドを含む)。
 
たとえば、以下のように指定したとします。

    X | join kind=fullouter Y on Key 

結果:

|キー |Value1 |キー |Value2 
|---|---|---|---
|a |1 |null |null 
|b |2 |b |10 
|b |3 |b |10 
|c |4 |c |20 
|c |4 |c |30 
|null |null |d |40 
 
### 左反結合 (kind=leftanti) 

左反結合では、右側のレコードと一致しない、左側のレコードがすべて返されます。
 
    X | join kind=leftanti Y on Key 
 
結果:

|キー |Value1 
|---|---
|a |1 
 
反結合では "NOT IN" クエリをモデル化します。



## インライン ディクショナリ マッピングとの結合

```AIQL

let TeamFoundationJobResult = range i from 1 to 1 step 1 
  | extend recordsJson = "[ 
    { 'key': -1, 'value': 'None'}, 
    { 'key': 0, 'value': 'Succeeded'}, 
    { 'key': 1, 'value': 'PartiallySucceeded'}, 
    { 'key': 2, 'value': 'Failed'}, 
    { 'key': 3, 'value': 'Stopped'}, 
    { 'key': 4, 'value': 'Killed'}, 
    { 'key': 5, 'value': 'Blocked'}, 
    { 'key': 6, 'value': 'ExtensionNotFound'}, 
    { 'key': 7, 'value': 'Inactive'}, 
    { 'key': 8, 'value': 'Disabled'}, 
    { 'key': 9, 'value': 'JobInitializationError'} 
  ]" 
  | mvexpand record = parsejson(replace("'", """, recordsJson)) 
  | project Result = toint(record.key), ResultString = tostring(record.value); 
JobHistory 
  | where PreciseTimeStamp > ago(1h)  
  | where Service  <> "AX" 
  | where Plugin has "Analytics"  
  | sort by PreciseTimeStamp desc 
  | join kind=leftouter TeamFoundationJobResult on Result 
  | extend ExecutionTimeSpan = totimespan(ExecutionTime) 
  | project JobName, StartTime, ExecutionTimeSpan, ResultString, ResultMessage  
```





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0330_2016-->