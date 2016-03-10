<properties
	pageTitle="JavaScript Web アプリのための Application Insights | Microsoft Azure"
	description="ページ ビューとセッション数、Web クライアントのデータを取得し、使用パターンを追跡します。JavaScript Web ページの例外とパフォーマンスの問題を検出します。"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/19/2016"
	ms.author="awills"/>

# Web ページ向けの Application Insights

[AZURE.INCLUDE [app-insights-selector-get-started-dotnet](../../includes/app-insights-selector-get-started-dotnet.md)]

Web ページまたはアプリのパフォーマンスと使用状況について調べます。Visual Studio Application Insights をページ スクリプトに追加すると、ページの読み込みや AJAX 呼び出しのタイミング、ブラウザーの例外や AJAX エラーの数と詳細のほか、ユーザー数やセッション数を取得することができます。いずれの情報も、ページ、クライアントの OS とブラウザー バージョン、geo ロケーションなどのディメンションごとにセグメント化することができます。エラーの数やページ読み込みの遅延に基づくアラートを設定することもできます。

短い JavaScript コードを追加するだけで、Application Insights はあらゆる Web ページで使用できます。Web サービスが [Java](app-insights-java-get-started.md) または [ASP.NET](app-insights-asp-net.md) である場合、サーバーとクライアントのテレメトリを統合することができます。

使用を開始するには、[Microsoft Azure](https://azure.com) のサブスクリプションが必要です。所属する部署がサブスクリプションを所有している場合、あなたの Microsoft アカウントをサブスクリプションに追加するようその所有者に依頼してください。無料の価格レベルが用意されているため、開発と小規模な用途であれば費用は一切かかりません。


## Web ページに Application Insights を設定する

必要な設定が既に済んでいる可能性もあります。アプリが新しい ASP.NET プロジェクトで、Visual Studio の [新しいプロジェクト] ダイアログから Application Insights を追加するように選択した場合、必要なスクリプトは既に追加されています。特別な設定は必要はありません。

それ以外の場合は、次の手順に従ってコード スニペットを Web ページに追加する必要があります。

### Application Insights リソースを開く

Application Insights リソースは、ページのパフォーマンスと使用状況に関するデータが表示される場所です。

[Azure ポータル](https://portal.azure.com)にサインインします。

アプリのサーバー側の監視を既に設定している場合は、既にリソースがあります。

![[参照]、[開発者向けサービス]、[Application Insights] の順に選択する](./media/app-insights-javascript/01-find.png)

リソースがない場合は、次の手順で作成します。

![[新規]、[開発者向けサービス]、[Application Insights] の順に選択する。](./media/app-insights-javascript/01-create.png)


*質問がございますか?* [リソースの作成に関する詳細はここにあります](app-insights-create-new-resource.md)。


### アプリや Web ページに SDK スクリプトを追加する

クイック スタートで、Web ページのスクリプトを取得します。

![アプリの概要ブレードで、クイック スタートを選択し、マイ Web ページを監視するためのコードを取得します。スクリプトをコピーします。](./media/app-insights-javascript/02-monitor-web-page.png)

追跡するすべてのページの `<head>` タグの直前にスクリプトを挿入します。Web サイトにマスター ページがある場合は、そこにスクリプトを配置できます。次に例を示します。

* ASP.NET MVC プロジェクトで、`View\Shared\_Layout.cshtml` にスクリプトを配置します。
* SharePoint サイトのコントロール パネルで、[[サイト設定 / マスター ページ](app-insights-sharepoint.md)] を開きます。

このスクリプトには、Application Insights リソースにデータを転送するインストルメンテーション キーが含まれています。

([スクリプトの詳細については、こちらを参照してください。](http://apmtips.com/blog/2015/03/18/javascript-snippet-explained/))

*(よく知られている Web ページ フレームワークを使用している場合、Application Insights アダプターを探してください。たとえば、[AngularJS モジュール](http://ngmodules.org/modules/angular-appinsights)があります。)*


## 詳細な構成

独自に設定できる[パラメーター](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)はいくつか存在しますが、ほとんどの場合、その必要はありません。たとえば、ページ ビューあたりの AJAX 呼び出し数の報告を無効にしたり、(トラフィックを抑制するために) 報告に上限を設けたりすることができます。また、テレメトリをまとめてパイプラインに送るのではなく随時送るようにデバッグ モードを設定することもできます。

これらのパラメーターを設定するには、コード スニペットから次の行を探し、その後ろにコンマ区切りで項目を追加します。

    })({
      instrumentationKey: "..."
      // Insert here
    });

[利用できるパラメーター](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)の例を次に示します。

    // Send telemetry immediately without batching.
    // Remember to remove this when no longer required, as it
    // can affect browser performance.
    enableDebug: true,

    // Don't log browser exceptions.
    disableExceptionTracking: true,

    // Limit number of Ajax calls logged, to reduce traffic.
    maxAjaxCallsPerView: 10, // default is 500

    // Set these dynamically for an authenticated user.
    appUserId: string,
    accountId: string,



## <a name="run"></a>アプリを実行する

Web アプリを実行し、しばらくの間、利用統計情報を生成し、少し待ちます。**F5** キーを使って開発用コンピューターで実行するか、公開し、ユーザーに利用させることができます。

Web アプリが Application Insights に送信している利用統計情報を確認する場合、ブラウザーのデバッグ ツールを使用します (多くのブラウザーで **F12** です)。データは dc.services.visualstudio.com に送信されます。

## ブラウザーのパフォーマンス データを調査する

ユーザーのブラウザーから集計したパフォーマンス データを表示するには、[ブラウザー] ブレードを開きます。

![In portal.azure.com, open your app's resource and click Settings, Browser](./media/app-insights-javascript/03.png)


*まだデータが表示されませんか? ページの上部にある **[更新]** をクリックします。まだは何も表示されませんか? 「[トラブルシューティング](app-insights-troubleshoot-faq.md)」を参照してください。*

[ブラウザー] ブレードは、あらかじめ設定されたフィルターと一連のグラフで構成された[メトリックス エクスプローラーのブレード](app-insights-metrics-explorer.md)です。時間範囲、フィルター、グラフの構成を必要に応じて編集し、その結果をお気に入りとして保存することができます。**[既定値に戻す]** をクリックすると、元のブレード構成に戻ります。

## ページ読み込みのパフォーマンス

最上部には、ページの読み込み時間をセグメント化したグラフが表示されます。グラフ全体の高さは、ユーザーのブラウザーでアプリからページを読み込んで表示するのにかかった平均時間を表します。時間の測定範囲は、ブラウザーが初期 HTTP 要求を送信してから、すべての同時読み込みイベントが処理されるまでとなります (レイアウトと実行中のスクリプトを含む)。AJAX 呼び出しからの Web パーツ読み込みといった非同期タスクは含まれません。

このグラフでは、ページ読み込み時間の合計が、[W3C で定義されている標準的なタイミング](http://www.w3.org/TR/navigation-timing/#processing-model)に合わせてセグメント化されています。

![](./media/app-insights-javascript/08-client-split.png)

通常、 *ネットワーク接続* 時間は予想より短くなることに注意してください。これは、ブラウザーからサーバーに送信されるすべての要求の平均であるためです。個別の要求の多くは接続時間が 0 です。サーバーへの接続が既にアクティブになっているためです。

### 読み込みに時間がかかる

ページの読み込みに時間がかかる問題は、エンド ユーザーの満足度を下げる大きな要因の 1 つです。グラフからページの読み込みに時間がかかっていることがわかれば、診断調査を簡単に行うことができます。

このグラフは、対象アプリにおける全ページ読み込みの平均時間を示しています。問題が特定のページに限定されているかどうかは、ブレードの下の方にある、ページの URL ごとにセグメント化されたグリッドで確認できます。

![](./media/app-insights-javascript/09-page-perf.png)

ページ ビュー カウントと標準偏差に注目してください。このページ カウントがごく小さければ、この問題はさほど多くのユーザーに影響していません。標準偏差が大きい (平均値そのものに匹敵するなど) ことは、個々の測定値にばらつきが多いことを示しています。

**特定の URL やページ ビューのクローズアップ。** いずれかのページ名をクリックすると、その URL だけを表示するようにブラウザー グラフがフィルタリングされてブレードが表示されます。そこからさらに、特定のページ ビューの情報だけを表示することもできます。

![](./media/app-insights-javascript/35.png)

`...` をクリックすると、該当するイベントの一連のプロパティがすべて表示されます。また、AJAX 呼び出しや関連するイベントを詳しく調査することもできます。AJAX 呼び出しが同期処理で実行されている場合、呼び出しに時間がかかると、ページ読み込み時間全体に影響します。関連するイベントとしては、同じ URL に対するサーバー要求があります (Web サーバーに対して Application Insights を設定した場合)。


**時間経過に伴うページ パフォーマンス。** 特定の時間帯にピークを迎えているかどうかを確認するには、[ブラウザー] ブレードに戻って、[ページ ビューの読み込み時間] グリッドを折れ線グラフに変更します。

![Click the head of the grid and select a new chart type](./media/app-insights-javascript/10-page-perf-area.png)

**セグメント化の基準となるディメンションの変更。** ページ読み込み速度の低下が、特定のブラウザーやクライアント OS、特定地域のユーザーに集中している可能性があります。 新しいグラフを追加し、**[グループ化の基準]** で適宜ディメンションを変えながら試してください。

![](./media/app-insights-javascript/21.png)


## AJAX パフォーマンス

Web ページの AJAX 呼び出しが適切に実行されていることを確認します。Ajax 呼び出しは、非同期的にページの一部を読み込む目的でよく使用されます。ページ全体としては即座に読み込まれていても、最初に空の Web パーツが表示され、データが表示されるまでに時間がかかっているようだと、ユーザーはストレスを感じる可能性があります。

Web ページからの AJAX 呼び出しは、[ブラウザー] ブレードに依存関係として表示されます。

ブレードの上部には、集計グラフが表示されます。

![](./media/app-insights-javascript/31.png)

詳細なグリッドは下の方に表示されます。

![](./media/app-insights-javascript/33.png)

いずれかの行をクリックすると、詳しい情報が表示されます。


> [AZURE.NOTE] ブレードで [ブラウザー] フィルターを削除した場合、サーバーと AJAX の両方の依存関係がこれらのグラフの対象となります。フィルターを再構成するには [既定値に戻す] をクリックしてください。

**AJAX 呼び出しエラーを詳しく調査する** 下にスクロールして [依存関係の障害] グリッドを表示し、特定の行をクリックしてその内容を表示します。

![](./media/app-insights-javascript/37.png)

`...` をクリックすると、AJAX 呼び出しの全テレメトリが表示されます。

### AJAX 呼び出しが報告されない場合

AJAX 呼び出しには、Web ページのスクリプトから実行されるすべての HTTP 呼び出しが含まれます。それらがまったく表示されない場合は、コード スニペットで `disableAjaxTracking` や `maxAjaxCallsPerView` [パラメーター](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)が設定されていないかどうかを確認してください。

## ブラウザーの例外

[ブラウザー] ブレードには、例外の集計グラフが表示されるほか、例外の種類を示すグリッドがブレードの下の方に表示されます。

![](./media/app-insights-javascript/39.png)

ブラウザーの例外がまったく表示されない場合は、コード スニペットで `disableExceptionTracking` [パラメーター](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#config)が設定されていないかどうかを確認してください。

## 個別のページ ビュー イベントを調査する

通常、Application Insights がページ ビューの利用統計情報を分析し、お客様に対して表示されるのは、すべてのユーザーに関して平均した累積レポートのみです。ただし、デバッグのために個別のページ ビュー イベントを調べることもできます。

[診断検索] ブレードで、[フィルター] を [ページ ビュー] に設定します。

![](./media/app-insights-javascript/12-search-pages.png)

いずれかのイベントをクリックして、詳細を表示します。詳細ページで、[...] をクリックしてさらに詳しい情報を表示します。

> [AZURE.NOTE] [検索](app-insights-diagnostic-search.md)を利用する場合、単語全体が一致しなければならないことに注意してください。「Abou」と「bout」は「About」に一致しませんが、「Abou*」は一致します。検索語をワイルドカードで始めることもできません。たとえば、「*bou」を検索しても「About」とは一致しません。

> [診断検索の詳細](app-insights-diagnostic-search.md)

### ページ ビュー プロパティ

* **ページ ビュー時間**: ページの読み込みや、スクリプト実行の開始にかかる時間です。具体的には、ページ読み込みの開始から trackPageView の実行までの間隔です。スクリプトの初期化後、trackPageView をその定位置から移動した場合、別の値が反映されます。


## ページ カウントのカスタマイズ

既定では、ページ カウントは新しいページがクライアント ブラウザーに読み込まれるたびに行われます。ただし、ページ ビューを別の場合にもカウントできます。たとえば、ページの内容がタブに表示され、ユーザーがタブを切り替えるときに対象ページをカウントしたい場合があります。またはページ内の JavaScript コードが、ブラウザーの URL を変更することなく新しいコンテンツを読み込む場合もあります。

次のような JavaScript 呼び出しをクライアント コードの適切な箇所に挿入します。

    appInsights.trackPageView(myPageName);

ページ名には、同じ文字を URL として含めることできますが、「#」または「?」の後の文字はすべて無視されます。



## 使用状況の追跡


アプリケーションで、ユーザーが何をするのかを知る必要がありますか。

* [使用状況の追跡について](app-insights-web-track-usage.md)
* [カスタム イベントとメトリックの API の詳細情報](app-insights-api-custom-events-metrics.md)。


#### <a name="video"></a> 動画: 使用状況の追跡

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a>次のステップ

* [利用状況を追跡する](app-insights-web-track-usage.md)
* [カスタム イベントとメトリックス](app-insights-api-custom-events-metrics.md)
* [ビルド - 測定 - 学習](app-insights-overview-usage.md)

<!----HONumber=AcomDC_0224_2016-->