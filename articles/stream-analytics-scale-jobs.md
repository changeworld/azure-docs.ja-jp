<properties title="Scale Azure Stream Analytics jobs" pageTitle="Stream Analytics ジョブのスケーリング | Azure" description="Stream Analytics ジョブのスケールを設定する方法について説明します。" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="10/28/2014" ms.author="jgao" />

# Azure Stream Analytics ジョブのスケーリング 

Stream Analytics ジョブの*ストリーミング ユニット*を計算する方法と、Stream Analytics ジョブをスケールするために入力パーティションの構成、クエリ定義のチューニング、およびジョブのストリーミング ユニットの設定を行う方法について説明します。

Azure Stream Analytics のジョブ定義は、入力、クエリ、および出力で構成されます。入力は、ジョブがそこからデータ ストリームを読み取る場所で、出力は、ジョブがジョブ結果を送信する宛先です。クエリは、入力ストリームを変換するために使用します。ジョブには少なくとも 1 つのデータ ストリームの入力ソースが必要です。データ ストリームの入力ソースには、Azure Service Bus Event Hub と Azure BLOB ストレージのいずれかを指定できます。詳細については、「[Azure Stream Analytics の概要][stream.analytics.introduction]」、「[Azure Stream Analytics を使ってみる][stream.analytics.get.started]」、および「[Azure Stream Analytics developer guide (Azure Stream Analytics 開発者ガイド)][stream.analytics.developer.guide]」を参照してください。 

Stream Analytics ジョブの処理に使用できるリソースは、ストリーミング ユニットで測定されます。各ストリーミング ユニットは最大 1 MB/秒のスループットを提供できます。各ジョブには最低 1 つのストリーミング ユニットが必要です。また、すべてのジョブに既定で 1 つが設定されています。Azure の管理ポータルを使用して、1 つの Stream Analytics ジョブに最大 12 個のストリーミング ユニットを設定できます。また、1 つの Azure サブスクリプションで設定できるストリーミング ユニットの最大数は、特定のリージョン内のすべてのジョブを合わせて 12 個です。サブスクリプションの最大ストリーミング ユニット数を 100 個に増やすには、[Microsoft サポート][microsoft.support]までご連絡ください。

ジョブで使用できるストリーミング ユニットの数は、入力のパーティション構成とジョブに定義されているクエリによって異なります。この記事では、スループットを向上させるための計算とクエリ チューニングの方法について説明します。

##この記事の内容
+ [ジョブのストリーミング ユニットの最大数を計算する](#calculate)
+ [Stream Analytics ジョブのパーティションを構成する](#configure)
+ [Stream Analytics ジョブのパフォーマンスを監視する](#monitor)
+ [次のステップ](#nextstep)


##<a name="calculate"></a>ジョブのストリーミング ユニットの最大数を計算する
Stream Analytics ジョブで使用できるストリーミング ユニットの合計数は、ジョブに定義されたクエリのステップ数と各ステップのパーティション数によって異なります。

### クエリのステップ
1 つのクエリに 1 つ以上のステップを含めることができます。各ステップは、WITH 句を使用して定義されたサブクエリです。WITH 句の範囲外にあるクエリは 1 つのみがステップとしてカウントされます。たとえば、次のクエリでは SELECT ステートメントが該当します。

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId 
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute,3), TollBoothId

上のクエリのステップは 2 つです。 

> [WACOM.NOTE] このサンプル クエリについては、記事の後半で説明します。

### ステップをパーティション分割する

ステップをパーティション分割するには、次の条件を満たす必要があります。

- 入力ソースをパーティション分割する。「[Azure Stream Analytics developer guide (Azure Stream Analytics 開発者ガイド)][stream.analytics.developer.guide]」および「[Event Hub プログラミング ガイド][azure.event.hubs.developer.guide]」を参照してください。
- クエリの SELECT ステートメントは、パーティション分割された入力ソースから読み取る。 
- ステップ内のクエリに Partition By 句を含める。 

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計され、そのグループごとに出力イベントが生成されます。結合された集計が望ましい場合は、パーティション分割されていない 2 つ目のステップを集計用に作成する必要があります。

Azure Stream Analytics のプレビュー リリースでは、列名でのパーティション分割をサポートしていません。パーティション分割は、クエリにあらかじめ組み込まれたフィールドである PartitionId フィールドでのみ実行できます。ParitionId フィールドは、ソース データ ストリームのどのパーティションからイベントが取得されるかを示しています。詳細については、「[Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]」を参照してください。

### ジョブのストリーミング ユニットの最大数を計算する

Stream Analytics ジョブのパーティション分割されていないステップは、最大 6 個のストリーミング ユニットにスケールできます。ストリーミング ユニットを追加するには、ステップをパーティション分割する必要があります。各パーティションには 6 個のストリーミング ユニットを設定できます。

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
<li>クエリに 2 つのステップが含まれている。</li>
<li>SELECT ステートメントはパーティション分割された入力から読み取る。</li>
</ul>
</td>
<td>24 (パーティション分割されたステップ用に 18 + パーティションされていないステップ用に 6)</td></tr>
</table>

###スケールの例
次のクエリは、3 つのブースがある高速道路の料金所を 3 分間に通過する車の台数を計算します。このクエリを最大 6 個のストリーミング ユニットにスケールできます。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

クエリに使用するストリーミング ユニットを増やすには、入力データ ストリームとクエリの両方をパーティション分割する必要があります。データ ストリームのパーティションが 3 つに設定されている場合、変更を加えた次のクエリを最大 18 個のストリーミング ユニットにスケールできます。

	SELECT COUNT(*) AS Count, TollBoothId
	FROM Input1 Partition By PartitionId
	GROUP BY TumblingWindow(minute, 3), TollBoothId

クエリがパーティション分割されている場合、入力イベントは処理されて個々のパーティション グループに集計され、そのグループごとに出力イベントが生成されます。GROUP BY フィールドが入力データ ストリームのパーティション キーでない場合、パーティション分割を実行すると予期しない結果になることがあります。たとえば、上のサンプル クエリの TollBoothId フィールドは Input1 のパーティション キーではありません。ブース 1 のデータは、複数のパーティションに分散できます。Input1 の各パーティションは Stream Analytics によって個別に処理され、同じタンブリング ウィンドウで同じ通過台数の複数のレコードが作成されます。入力パーティション キーを変更できない場合は、パーティション分割されていないステップを追加することで、この問題を解決できます。次に例を示します。

	WITH Step1 (
		SELECT COUNT(*) AS Count, TollBoothId
		FROM Input1 Partition By PartitionId
		GROUP BY TumblingWindow(minute, 3), TollBoothId
	) 

	SELECT SUM(Count) AS Count, TollBoothId
	FROM Step1 
	GROUP BY TumblingWindow(minute, 3), TollBoothId

このクエリは 24 個のストリーミング ユニットにスケールできます。 

>[WACOM.NOTE] 2 つのストリームを結合している場合は、結合を実行した列のパーティション キーでストリームがパーティション分割され、両方のストリームに同じ数のパーティションが作成されていることを確認してください。


##<a name="configure"></a>Stream Analytics ジョブのパーティションを構成する

**ジョブのストリーミング ユニットを調整するには**

1. [管理ポータル][azure.management.portal]にサインインします。
2. 左側の **[Stream Analytics]** をクリックします。
3. スケールする Stream Analytics ジョブをクリックします。
4. ページの上部にある **[スケール]** をクリックします。

![Azure Stream Analytics configure job scale][img.stream.analytics.configure.scale]


##<a name="monitor"></a>ジョブのパフォーマンスを監視する

管理ポータルを使用して、ジョブのスループット (イベント数/秒) を追跡できます。

![Azure Stream Analytics monitor jobs][img.stream.analytics.monitor.job]
 
ワークロードのスループット予測 (イベント数/秒) を計算します。スループットが予測よりも小さい場合、入力パーティションのチューニングとクエリのチューニングを実行し、ジョブにストリーミング ユニットを追加してください。

##<a name="nextstep"></a> 次のステップ
この記事では、ストリーミング ユニットを計算する方法と Stream Analytics ジョブをスケールする方法について説明しました。Stream Analytics の詳細については、次のトピックに関するページを参照してください。

- [Azure Stream Analytics の概要][stream.analytics.introduction]
- [Azure Stream Analytics を使ってみる][stream.analytics.get.started]
- [Azure Stream Analytics 開発者ガイド][stream.analytics.developer.guide]
- [Azure Stream Analytics の制限事項と既知の問題][stream.analytics.limitations]
- [Azure Stream Analytics クエリ言語リファレンス][stream.analytics.query.language.reference]
- [Azure Stream Analytics 管理用 REST API リファレンス][stream.analytics.rest.api.reference]

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/ja-jp/library/azure/dn789972.aspx

[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


