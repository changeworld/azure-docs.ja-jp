<properties
	pageTitle="Stream Analytics ジョブのスケーリング | Azure"
	description="Stream Analytics ジョブのスケールを設定する方法について説明します。"
	services="stream-analytics"
	documentationCenter=""
	authors="jeffstokes72"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="stream-analytics"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="04/28/2015"
	ms.author="jeffstok"/>

# Azure Stream Analytics ジョブのスケーリング

Stream Analytics ジョブの*ストリーミング ユニット*を計算する方法と、Stream Analytics ジョブをスケールするために入力パーティションの構成、クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。

Azure Stream Analytics のジョブ定義は、入力、クエリ、および出力で構成されます。入力は、ジョブがデータ ストリームを読み取る場所です。クエリは、入力ストリームを変換するために使用します。出力は、ジョブがジョブ結果を送信する宛先です。

ジョブには少なくとも 1 つのデータ ストリームの入力ソースが必要です。データ ストリームの入力ソースは、Azure Service Bus Event Hub または Azure BLOB ストレージに格納できます。詳細については、「[Azure Stream Analytics の概要](stream-analytics-introduction.md)」、「[Azure Stream Analytics の使用](stream-analytics-get-started.md)」、および「[Azure Stream Analytics 開発者ガイド](../stream-analytics-developer-guide.md)」を参照してください。

Stream Analytics ジョブの処理に使用できるリソースは、ストリーミング ユニットで測定されます。各ストリーミング ユニットは最大 1 MB/秒のスループットを提供できます。各ジョブには最低 1 つのストリーミング ユニットが必要です。また、すべてのジョブに既定で 1 つが設定されています。Azure ポータルを使用して、1 つの Stream Analytics ジョブに最大 50 個のストリーミング ユニットを設定できます。また、1 つの Azure サブスクリプションで設定できるストリーミング ユニットの最大数は、特定のリージョン内のすべてのジョブを合わせて 50 個です。サブスクリプションのストリーミング ユニット数を増やすには (最大で 100 個)、[Microsoft サポート](http://support.microsoft.com)までご連絡ください。

ジョブで使用できるストリーミング ユニットの数は、入力のパーティション構成とジョブに定義されているクエリによって異なります。この記事では、スループットを向上させるための計算とクエリ チューニングの方法について説明します。


## ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブで使用できるストリーミング ユニットの合計数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。

### クエリでのステップ
1 つのクエリに 1 つ以上のステップを含めることができます。各ステップは、WITH 句を使用して定義されたサブクエリです。WITH 句の範囲外にある唯一のクエリもステップと見なされます。たとえば、以下のクエリの SELECT ステートメントは、ステップと見なされます。

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute,3), TollBoothId, PartitionId

上のクエリのステップは 2 つです。

> [AZURE.NOTE]このサンプル クエリについては、記事の後半で説明します。

### ステップをパーティション分割する

ステップをパーティション分割するには、次の条件を満たす必要があります。

- 入力ソースはパーティション分割する。詳細については、「[Azure Stream Analytics 開発者ガイド](../stream-analytics-developer-guide.md)」と「[Event Hub プログラミング ガイド](../azure-event-hubs-developer-guide.md)」を参照してください。
- クエリの SELECT ステートメントは、パーティション分割された入力ソースから読み取る。
- ステップ内のクエリに **Partition By** 句を含める。

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計され、そのグループごとに出力イベントが生成されます。結合された集計が望ましい場合は、パーティション分割されていない 2 つ目のステップを集計用に作成する必要があります。

### ジョブのストリーミング ユニットの最大数を計算する

Stream Analytics ジョブのパーティション分割されていないステップは、最大 6 個のストリーミング ユニットにスケールアップできます。ストリーミング ユニットを追加するには、ステップをパーティション分割する必要があります。各パーティションには 6 個のストリーミング ユニットを設定できます。

<table border="1">
<tr><th>ジョブのクエリ</th><th>ジョブの最大ストリーミング ユニット数</th></td>

<tr><td>
<ul>
<li>クエリに 1 つのステップが含まれている。</li>
<li>ステップはパーティション分割されていない。</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>入力データ ストリームは 3 つにパーティション分割されている。</li>
<li>クエリに 1 つのステップが含まれている。</li>
<li>ステップはパーティション分割されていない。</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>クエリに 2 つのステップが含まれている。</li>
<li>どのステップもパーティション分割されていない。</li>
</ul>
</td>
<td>6</td></tr>



<tr><td>
<ul>
<li>入力データ ストリームは 3 つにパーティション分割されている。</li>
<li>クエリに 2 つのステップが含まれている。入力ステップはパーティション分割されているが、2 番目のステップはされていない。</li>
<li>SELECT ステートメントはパーティション分割された入力から読み取る。</li>
</ul>
</td>
<td>24 (パーティション分割されたステップ用に 18 + パーティションされていないステップ用に 6)</td></tr>
</table>

### スケールの例
次のクエリは、3 つのブースがある高速道路の料金所を 3 分間に通過する車の台数を計算します。このクエリを最大 6 個のストリーミング ユニットにスケールアップできます。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリに使用するストリーミング ユニットを増やすには、入力データ ストリームとクエリの両方をパーティション分割する必要があります。データ ストリームのパーティションが 3 つに設定されている場合、変更を加えた次のクエリを最大 18 個のストリーミング ユニットにスケールアップできます。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計されます。出力イベントは、それぞれのグループに対しても生成されます。**Group-by** フィールドが入力データ ストリームのパーティション キーでない場合、パーティション分割を実行すると予期しない結果になることがあります。たとえば、上のサンプル クエリの TollBoothId フィールドは Input1 のパーティション キーではありません。TollBooth 1 のデータは、複数のパーティションに分散できます。

Input1 の各パーティションは Stream Analytics によって個別に処理され、同じタンブリング ウィンドウで同じ料金所の複数の通過台数レコードが作成されます。入力パーティション キーを変更できない場合は、パーティション分割されていないステップを追加することで、この問題を解決できます。たとえば次のようにします。

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
	)

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1
	GROUP BY TumblingWindow(minute, 3), TollBoothId, ParititonId

このクエリは 24 個のストリーミング ユニットにスケールできます。

>[AZURE.NOTE]2 つのストリームを結合している場合は、結合を実行した列のパーティション キーでストリームがパーティション分割され、両方のストリームに同じ数のパーティションが作成されていることを確認してください。


## Stream Analytics ジョブのパーティションを構成する

**ジョブのストリーミング ユニットを調整するには**

1. [管理ポータル](https://manage.windowsazure.com)にサインインします。
2. 左側のウィンドウで **[Stream Analytics]** をクリックします。
3. スケールする Stream Analytics ジョブをクリックします。
4. ページの上部にある **[スケール]** をクリックします。

![Azure Stream Analytics のジョブのスケーリングの構成][img.stream.analytics.configure.scale]


## ジョブのパフォーマンスを監視する

管理ポータルを使用して、ジョブのスループット (イベント数/秒) を追跡できます。

![Azure Stream Analytics のジョブの監視][img.stream.analytics.monitor.job]

ワークロードのスループット予測 (イベント数/秒) を計算します。スループットが予測よりも小さい場合、入力パーティションのチューニングとクエリのチューニングを実行し、ジョブにストリーミング ユニットを追加してください。

##スケールごとの ASA のスループット - Raspberry Pi のシナリオ


複数のストリーミング ユニット間での処理スループットを考察する標準的なシナリオで、ASA がどのようにスケールするかを理解するために、ここでセンサー データ (クライアント) を Event Hub に送信し、ASA がそのデータを処理して、別の Event Hub に出力としてアラートまたは統計を送信するというテストをします。

クライアントは ASA 向けの合成されたセンサー データを  JSON 形式で Event Hubs に送信し、ASA からのデータ出力も JSON 形式とします。サンプル データは次のようになります。

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

クエリ: "電灯がオフになった場合にアラートを送信する"

    SELECT AVG(lght),
	 “LightOff” as AlertText
	FROM input TIMESTAMP
	BY devicetime
	 WHERE
		lght< 0.05 GROUP BY TumblingWindow(second, 1)

スループットの測定: このコンテキストではスループットは、固定時間内 (10 分間) で ASA が処理した入力データの量です。入力データの最適な処理スループットを実現するために、入力データ ストリームとクエリの両方をパーティション分割する必要があります。さらに、入力イベントの処理数を測定するために、COUNT() をクエリに含める必要があります。ASA が単に着信する入力イベントを待機しているだけとならないように、入力 Event Hub の各パーティションには、十分な入力データ (約 300 MB) が事前に読み込まれています。

以下の結果は、Event Hubs でのストリーミング ユニット数の増加に対応するパーティション数を示しています。

<table border="1">
<tr><th>入力パーティション数</th><th>出力パーティション数</th><th>ストリーミング ユニット数</th><th>持続スループット
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4.06 MB/秒</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8.06 MB/秒</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38.32 MB/秒</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172.67 MB/秒</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454.27 MB/秒</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609.69 MB/秒</td>
</tr>
</table>

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## 問い合わせ
さらにサポートが必要な場合は、[Azure Stream Analytics フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)を参照してください。


## 次のステップ

- [Azure Stream Analytics の概要](stream-analytics-introduction.md)
- [Azure Stream Analytics の使用](stream-analytics-get-started.md)
- [Azure Stream Analytics ジョブのスケーリング](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (Stream Analytics クエリ言語リファレンス)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Azure Stream Analytics management REST API reference (Azure ストリーム分析の管理 REST API リファレンス)](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.limitations]: ../stream-analytics-limitations.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=54--> 