<properties 
	pageTitle="診断検索の使用" 
	description="個々のイベント、要求、ログ トレースを検索し、フィルター選択します。" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/22/2015" 
	ms.author="awills"/>
 
# Application Insights の診断検索の使用

[診断検索] は、ページ ビュー、例外、Web 要求などの個々のテレメトリ項目を検索または探索するために使用する [Application Insights][start] のブレードです。診断検索を使用すると、作成したログ トレースやイベントを表示できます。

## 診断検索が表示されるタイミング

診断検索は、明示的に開くことができます。

![Open diagnostic search](./media/app-insights-diagnostic-search/01-open-Diagnostic.png)


診断検索は、特定のグラフやグリッド項目をクリックしたときにも表示されます。この場合、選択した項目の種類に合わせてフィルターが事前設定されます。

たとえば、アプリケーションが Web サービスの場合は、概要ブレードに要求の量のグラフが表示されます。このグラフをクリックすると、各 URL に対する要求の数を示すより詳細なグラフが表示されます。いずれかの行をクリックすると、その URL に対する個々の要求の一覧が表示されます。

![Open diagnostic search](./media/app-insights-diagnostic-search/07-open-from-filters.png)


診断検索の本体は、テレメトリ項目 (サーバー要求、ページ ビュー、お客様が作成したカスタム イベントなど) の一覧です。一覧の上部には、一定期間に発生したイベントの数を示す概要グラフが表示されます。


## 個々の項目の確認

任意のテレメトリ項目を選択すると、キー フィールドと関連項目が表示されます。フィールドの完全なセットを表示するには、[...] をクリックします。

![Open diagnostic search](./media/app-insights-diagnostic-search/10-detail.png)

フィールドの完全なセットを検索するには、ワイルドカードを含まない単純な文字列を使用します。使用できるフィールドは、テレメトリの種類に依存します。

## イベントの種類のフィルター選択

[フィルター] ブレードを開き、表示するイベントの種類を選択します (後でブレードを開いたときに表示されるフィルターを復元するには、[リセット] をクリックします)。


![[フィルター] を選択し、テレメトリの種類を選択します](./media/app-insights-diagnostic-search/02-filter-req.png)


イベントの種類は、次のとおりです。

* **[トレース]** - 診断ログ (TrackTrace、log4Net、NLog、System.Diagnostic.Trace の呼び出しなど)。
* **[要求]** - サーバー アプリケーションで受信した HTTP 要求 (ページ、スクリプト、画像、スタイル ファイル、データなど)。これらのイベントは、要求と応答の概要グラフの作成に使用されます。
* **[ページ ビュー]** - Web クライアントによって送信されたテレメトリ。ページ ビュー レポートの作成に使用されます。 
* **[カスタム イベント]** - [利用状況の監視][track]のために TrackEvent() への呼び出しを挿入した場合は、ここで検索できます。
* **[例外]** - TrackException() を使用してログに記録した、サーバーでキャッチされていない例外。

## プロパティの値に基づくフィルター選択

プロパティの値に基づいてイベントをフィルター選択できます。使用できるプロパティは、選択したイベントの種類に依存します。

たとえば、特定の応答コードを持つ要求を選択できます。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/03-response500.png)

特定のプロパティの値を選択しない場合、すべての値を選択するのと同じ意味になり、そのプロパティに基づくフィルターはオフになります。


### 検索の絞り込み

フィルター値の右側の値は、現在のフィルター選択されたセットに含まれるイベントの発生回数を表します。

この例では、500 エラーの大部分が `Reports/Employees` 要求であることがわかります。

![プロパティを展開し、値を選択します](./media/app-insights-diagnostic-search/04-failingReq.png)

さらに、この期間中に発生している他のイベントを確認するには、**[未定義のプロパティを持つイベントを含める]** チェック ボックスをオフにします。

## 個々の発生の確認

この要求の名前をフィルター セットに追加して、そのイベントの個々の発生を確認できます。

![値を選択します](./media/app-insights-diagnostic-search/05-reqDetails.png)

要求イベントの場合、要求の処理中に発生した例外が詳細情報として表示されます。

例外をクリックすると、詳細が表示されます。

![例外をクリックします](./media/app-insights-diagnostic-search/06-callStack.png)

## 同じプロパティを持つイベントの検索

同じプロパティ値を持つすべての項目を検索できます。

![プロパティを右クリックします](./media/app-insights-diagnostic-search/12-samevalue.png)

## メトリック値に基づく検索

応答時間が 5 秒を超えるすべての要求を取得します。時間はティック単位で表されます。10,000 ティックが 1 ミリ秒です。

!["Response time":(threshold TO *)](./media/app-insights-diagnostic-search/11-responsetime.png)



## <a name="search"></a>データの検索

すべてのプロパティ値について語句を検索できます。この機能は、プロパティ値を持つ[カスタム イベント][track]を作成している場合に特に便利です。

時間範囲を設定することもできます。範囲が狭いほど、検索時間が短くなります。

![Open diagnostic search](./media/app-insights-diagnostic-search/appinsights-311search.png)

部分文字列ではなく語句を検索することに注意してください。語句とは、"."、"_" などのいくつかの句読文字を含む、英数字の文字列です。次に例を示します。

語句|一致*しない*|一致する
---|---|---
HomeController.About|about<br/>home|h*about<br/>home*
IsLocal|local<br/>is<br/>*local|isl*<br/>islocal<br/>i*l*
New Delay|w d|new<br/>delay<br/>n* AND d*


使用できる検索表現を次に示します。

サンプル クエリ | 効果 
---|---
slow|日付範囲内でフィールドに "slow" という語句が含まれるすべてのイベントを検索します
database??|database01、databaseAB などに一致します。<br/>? は、検索語句の先頭に使用できません。
database*|database、database01、databaseNNNN に一致します。<br/>* は、検索語句の先頭に使用できません。
apple AND banana|両方の語句を含むイベントを検索します。"and" ではなく大文字の "AND" を使用します。
apple OR banana<br/>apple banana|どちらかの語句を含むイベントを検索します。"or" ではなく "OR" を使用します。</br/>短縮形。
apple NOT banana<br/>apple -banana|ある用語を含む一方で他方の用語を含まないイベントを検索します。<br/>短縮形。
app* AND banana -(grape pear)|論理演算子とかっこの使用。
"Metric": 0 TO 500<br/>"Metric" : 500 TO * | 値範囲内の指定された測定値を含むイベントを検索します。


## 検索条件の保存

必要なフィルターをすべて設定した後、この検索条件をお気に入りとして保存できます。組織のアカウントで作業している場合は、これを他のチーム メンバーと共有するかどうかを選択できます。

![[お気に入り] をクリックし、名前を設定して [保存] をクリックします](./media/app-insights-diagnostic-search/08-favorite-save.png)


検索条件をもう一度表示するには、**概要ブレードに移動**し、[お気に入り] を開きます。

![[お気に入り] タイル](./media/app-insights-diagnostic-search/09-favorite-get.png)

相対的な時間範囲を選択して保存した場合は、ブレードを開くたびに最新のデータが表示されます。絶対的な時間範囲を選択して保存した場合は、毎回同じデータが表示されます。


## さらに多くのテレメトリを Application Insights に送信する

Application Insights SDK によって送信される標準のテレメトリに加えて、次の操作を実行できます。

* [.NET][netlogs] または [Java][javalogs] の好みのログ フレームワークからのログ トレースをキャプチャする。これは、ログ トレースを検索し、ページ ビュー、例外、その他のイベントと関連付けることができることを意味します。 
* カスタム イベント、ページ ビュー、例外を送信する[コードを作成][track]する。 

[ログとカスタム テレメトリを Application Insights に送信する方法についてはこちら][trace]。


## <a name="questions"></a>Q & A

### <a name="limits"></a>保持されるデータの量はどのくらいですか

各アプリケーションで、1 秒あたり 500 イベントまでです。イベントは 7 日間保持されます。

### サーバーの要求の POST データを表示するにはどうしたらよいですか

POST データは自動的に記録されませんが、[TrackTrace または log の呼び出し][trace]を使用できます。メッセージ パラメーターに POST データを格納します。プロパティと同じ方法でメッセージをフィルター選択することはできませんが、サイズの制限が緩和されます。

## <a name="add"></a>次のステップ

* [Application Insights にログとカスタム テレメトリを送信する][trace]
* [可用性と応答性のテストを設定する][availability]
* [トラブルシューティング][qna]





<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[javalogs]: app-insights-java-trace-logs.md
[netlogs]: app-insights-asp-net-trace-logs.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-get-started.md
[trace]: app-insights-search-diagnostic-logs.md
[track]: app-insights-custom-events-metrics-api.md


<!--HONumber=54--> 