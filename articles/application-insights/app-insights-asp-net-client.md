<properties 
	pageTitle="Application Insights での依存関係の追跡" 
	description="オンプレミスまたは Microsoft Azure Web アプリケーションの使用状況、可用性、パフォーマンスを Application Insights で分析します。" 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/08/2015" 
	ms.author="awills"/>


# Application Insights の設定: Web ページ

*Application Insights はプレビュー段階です。*


[Visual Studio Application Insights](http://azure.microsoft.com/services/application-insights) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断][detect]、[アプリの使用方法の把握][knowUsers]に役立ちます。


<a name="selector1"></a>

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]


Web ページのパフォーマンスと使用状況について調べます。Visual Studio Application Insights をページに追加すると、利用者の数、再訪問の頻度、最も利用されているページがわかります。読み込み時間と例外のレポートも与えられます。いくつかの[カスタム イベントとメトリック][api]を追加すると、最も人気のある機能や最も一般的なミスを詳細に分析し、ユーザーが満足するようにページを調整できます。

![[新規]、[開発者向けサービス]、[Application Insights] の順に選択する。](./media/app-insights-asp-net-client/16-page-views.png)

[ASP.NET](app-insights-asp-net.md) または [Java](app-insights-java-get-started.md) Web アプリにサーバー テレメトリを既に設定している場合、クライアントとサーバーの両方の角度からの画像が与えられます。この 2 つのストリームは Application Insights ポータルで統合されます。

## Application Insights リソースを開く

[Azure ポータル](http://portal.azure.com)にサインインします。

アプリのサーバー側の監視を既に設定している場合は、既にリソースがあります。

![[参照]、[開発者向けサービス]、[Application Insights] の順に選択する](./media/app-insights-asp-net-client/01-find.png)

リソースがない場合は、次の手順で作成します。

![[新規]、[開発者向けサービス]、[Application Insights] の順に選択する。](./media/app-insights-asp-net-client/01-create.png)


## アプリや Web ページに SDK スクリプトを追加する

クイック スタートで、Web ページのスクリプトを取得します。

![アプリの概要ブレードで、クイック スタートを選択し、マイ Web ページを監視するためのコードを取得します。スクリプトをコピーします。](./media/app-insights-asp-net-client/02-monitor-web-page.png)

追跡するすべてのページの &lt;/head&gt; タグの直前に、スクリプトを挿入します。Web サイトにマスター ページがある場合は、そこにスクリプトを配置できます。次に例を示します。

* ASP.NET MVC プロジェクトで、View\\Shared\\_Layout.cshtml にスクリプトを配置します。
* SharePoint サイトのコントロール パネルで、[[サイト設定 / マスター ページ](app-insights-sharepoint.md)] を開きます。

このスクリプトには、Application Insights リソースにデータを転送するインストルメンテーション キーが含まれています。

*(よく知られている Web ページ フレームワークを使用している場合、Application Insights アダプターを探してください。たとえば、[AngularJS モジュール](http://ngmodules.org/modules/angular-appinsights)があります。)*


## <a name="run"></a>アプリを実行する

Web アプリを実行し、しばらくの間、テレメトリを生成し、少し待ちます。**F5** キーを使って開発用コンピューターで実行するか、公開されたものをユーザーが利用することができます。

Web アプリが Application Insights に送信しているテレメトリを確認する場合、ブラウザーのデバッグ ツールを使用します (多くのブラウザーで **F12** です)。データは dc.services.visualstudio.com に送信されます。

## データを調査する

アプリケーションの概要ブレードでは、最上部の近くにグラフがあり、ブラウザーにページを読み込むときの平均時間が表示されます。


![](./media/app-insights-asp-net-client/05-browser-page-load.png)


*まだデータが表示されませんか? ページの上部にある **[更新]** をクリックします。まだは何も表示されませんか? 「[トラブルシューティング][qna]」を参照してください。*

そのグラフをクリックすると、さらに詳しいグラフになって表示されます。

![](./media/app-insights-asp-net-client/07-client-perf.png)

これは積み重ねグラフであり、合計ページ読み込み時間を [W3C が定義する標準のタイミング](http://www.w3.org/TR/navigation-timing/#processing-model)に分解します。

![](./media/app-insights-asp-net-client/08-client-split.png)

通常、*ネットワーク接続*時間は予想より短くなることに注意してください。これは、ブラウザーからサーバーに送信されるすべての要求の平均であるためです。個別の要求の多くは接続時間が 0 です。サーバーへの接続が既にアクティブになっているためです。


### ページ別のパフォーマンス

詳細ブレードのさらに下に、ページ URL で分割されたグリッドがあります。


![](./media/app-insights-asp-net-client/09-page-perf.png)

時間の経過と共に変化するページのパフォーマンスを表示する場合は、グリッドをダブルクリックし、グラフの種類を変更します。

![](./media/app-insights-asp-net-client/10-page-perf-area.png)

## クライアント使用状況の概要

概要ブレードに戻り、**[使用状況]** をクリックします。

![](./media/app-insights-asp-net-client/14-usage.png)

* **ユーザー:** グラフの時間範囲内で変化する一意のユーザーの数。(戻ってくるユーザーを識別するために Cookie が使用されます)。
* **セッション:** ユーザーが 30 分間何の要求も出さないと、セッションがカウントされます。
* **ページ ビュー**: trackPageView() の呼び出し数をカウントします。通常、各 Web ページで 1 回呼び出されます。


### クリックして詳細を表示する

グラフをクリックすると、詳細が表示されます。グラフの時間範囲を変更できます。

![](./media/app-insights-asp-net-client/appinsights-49usage.png)


グラフをクリックして表示するメトリックを選択するか、新しいグラフを追加して表示するメトリックを選択します。

![](./media/app-insights-asp-net-client/appinsights-63usermetrics.png)

> [AZURE.NOTE]メトリックは、いくつかの組み合わせでしか表示できないことがあります。メトリックを選択すると、互換性のないメトリックは無効になります。



## ページ カウントのカスタマイズ

既定では、ページ カウントは新しいページがクライアント ブラウザーに読み込まれるたびに行われます。ただし、ページ ビューを別の場合にもカウントできます。たとえば、ページの内容がタブに表示され、ユーザーがタブを切り替えるときに対象ページをカウントしたい場合があります。またはページ内の JavaScript コードが、ブラウザーの URL を変更することなく新しいコンテンツを読み込む場合もあります。

次のような JavaScript 呼び出しをクライアント コードの適切な箇所に挿入します。

    appInsights.trackPageView(myPageName);

ページ名には、同じ文字を URL として含めることできますが、「#」または「?」の後の文字はすべて無視されます。


## 個別のページ ビュー イベントを調査する

通常、Application Insights がページ ビューのテレメトリを分析し、お客様に対して表示されるのは、すべてのユーザーに関して平均した累積レポートのみです。ただし、デバッグのために個別のページ ビュー イベントを調べることもできます。

[診断検索] ブレードで、[フィルター] を [ページ ビュー] に設定します。

![](./media/app-insights-asp-net-client/12-search-pages.png)

いずれかのイベントをクリックして、詳細を表示します。詳細ページで、[...] をクリックしてさらに詳しい情報を表示します。

> [AZURE.NOTE] [検索][diagnostic]を利用する場合、単語全体が一致しなければならないことに注意してください。「Abou」と「bout」は「About」に一致しませんが、「Abou*」は一致します。検索語をワイルドカードで始めることもできません。たとえば、「*bou」を検索しても「About」とは一致しません。

> [診断検索の詳細][diagnostic]

### ページ ビュー プロパティ

* **ページ ビュー時間**: ページの読み込みや、スクリプト実行の開始にかかる時間です。具体的には、ページ読み込みの開始から trackPageView の実行までの間隔です。スクリプトの初期化後、trackPageView をその定位置から移動した場合、別の値が反映されます。

## 使用状況の追跡 (カスタム)

アプリケーションで、ユーザーが何をするのかを知る必要がありますか。 クライアントとサーバーのコードに呼び出しを挿入すると、Application Insights に独自のテレメトリを送信できます。たとえば、注文を作成したが完了していないユーザーの数、最も頻繁に発生する検証エラー、またはゲームの平均スコアを確認できます。

* [カスタム イベントとメトリックの API の詳細情報][api]。
* [API リファレンス](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)




## <a name="video"></a> 動画: 使用状況の追跡

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a> 次のステップ

- [SDK のインストール](../article/application-insights/app-insights-asp-net.md#selector1)
- [カスタム イベントとメトリックを使用した利用状況の追跡][api]
- [可用性](../article/application-insights/app-insights-monitor-web-app-availability.md#selector1)
- [既にライブのアプリ](../article/application-insights/app-insights-monitor-performance-live-website-now.md)

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-asp-net-client.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md


 

<!----HONumber=Oct15_HO3-->