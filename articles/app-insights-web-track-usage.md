<properties title="Track usage in web applications with Application Insights" pageTitle="Web アプリケーションの利用状況の追跡" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# Web アプリケーションの使用状況の追跡

Web アプリケーションがどのように使用されているのかを確認します。利用状況分析を設定すると、ユーザーが閲覧しているページ、再訪するユーザーの数、およびユーザーがサイトを閲覧する頻度を確認できます。いくつかの[カスタム イベントおよびメトリック][track]を追加すると、最も人気のある機能や最も一般的なミスを詳細に分析し、ユーザーが満足するようアプリを調整できます。

利用統計情報は、クライアントとサーバーの両方から収集されます。クライアントのデータは、すべての最新の Web ブラウザーから収集され、使用しているプラットフォームが ASP.NET の場合は、サーバーのデータを収集できます (サーバーは、Azure で実行されていなくてもかまいません)。 

* [Web 利用状況分析の設定](#webclient)
* [利用状況分析](#usage)
* [単一ページ アプリケーションのカスタムのページ数](#spa)
* [個々のページ イベントの検査](#inspect)
* [カスタム イベントおよびメトリックを使用した詳細な追跡](#custom)
* [ビデオ](#video)

## <a name="webclient"></a>Web クライアントの分析の設定

#### Azure で Application Insights のリソースを取得する

**ASP.NET アプリケーションを開発する場合で**、まだこれを実行していないときは、[Web プロジェクトに Application Insights を追加][start]します。 

**Web サイト プラットフォームが ASP.NET でない場合:**[Microsoft Azure](http://azure.com) にサインアップし、[プレビュー ポータル](https://portal.azure.com)に移動して Application Insights のリソースを追加します。

![](./media/appinsights/appinsights-11newApp.png)

([参照] ボタンを使用して、後でここに戻ることができます。)



#### Web ページに、スクリプトを追加する

クイック スタートで、Web ページのスクリプトを取得します。

![](./media/appinsights/appinsights-06webcode.png)

追跡するすべてのページの </head> タグの直前に、スクリプトを挿入します。Web サイトにマスター ページがある場合は、そこにスクリプトを配置できます。たとえば、ASP.NET MVC プロジェクトで、View\Shared\_Layout.cshtml にスクリプトを配置します。

## <a name="usage"></a>利用状況分析

Web サイトを実行し、少し使用して利用統計情報を生成します。その後 1 ～ 2 分間待機します。Web サイトは、F5 キーを使って開発用コンピューターで実行するか、サーバーにデプロイできます。

[アプリケーション概要] ブレードには、以下の利用状況タイルが表示されます。

![](./media/appinsights/appinsights-47usage.png)

*まだデータが表示されませんか。ページの上部にある **[更新]** をクリックします。*

* **ブラウザーごとのセッション数**

    *セッション*とは、ユーザーがご使用の Web サイト上のページを開いてから、Web 要求をまったく送信せずに 30 分のタイムアウト期間が終了するまでの期間のことです。 

    クリックして、グラフを拡大表示します。

* **上位ページ ビュー**

    過去 24 時間の合計数を示します。

    [ページ ビュー] タイルをクリックして、詳細履歴を取得します。

![](./media/appinsights/appinsights-49usage.png)

[時間の範囲] をクリックして、最長で 7 日間の履歴を表示します。

グラフをクリックし、表示可能な他のメトリックを表示します。

![](./media/appinsights/appinsights-63usermetrics.png)

> [AZURE.NOTE] これらをすべて有効にするメトリックを*すべて*オフにします。メトリックは、いくつかの組み合わせでしか表示できないことがあります。メトリックを選択すると、互換性のないメトリックは無効になります。



## <a name="spa"></a>単一ページ アプリケーションのカスタムのページ数

既定では、ページ カウントは新しいページがクライアント ブラウザーに読み込まれるたびに行われます。ただし、ページ ビューを別の場合にもカウントできます。たとえば、ページの内容がタブに表示され、ユーザーがタブを切り替えるときに対象ページをカウントしたい場合があります。またはページ内の JavaScript コードが、ブラウザーの URL を変更することなく新しいコンテンツを読み込む場合もあります。 

次のような JavaScript 呼び出しをクライアント コードの適切な箇所に挿入します。

    appInsights.trackPageView(myPageName);

ページ名には、同じ文字を URL として含めることできますが、「#」または「?」の後の文字はすべて無視されます。


## <a name="inspect"></a>各ページ ビュー イベントの調査

通常、Application Insights がページ ビューの利用統計情報を分析し、お客様に対して表示されるのは、すべてのユーザーに関して平均した累積レポートのみです。ただし、デバッグのために個別のページ ビュー イベントを調べることもできます。

[診断検索] ブレードで、[フィルター] を [ページ ビュー] に設定します。

![](./media/appinsights/appinsights-51searchpageviews.png)

いずれかのイベントをクリックして、詳細を表示します。

> [AZURE.NOTE] [検索][diagnostic]を使用する場合、単語全体が完全に一致する必要があります。「Abou」と「bout」は「About」とは一致しませんが、「Abou*」は一致します。検索語をワイルドカードで始めることもできません。たとえば、「*bou」を検索しても「About」とは一致しません。 

> [診断検索の詳細][diagnostic]

## <a name="custom"></a>カスタム イベントおよびメトリックを使用した詳細な追跡

アプリケーションで、ユーザーが何をするのかを知る必要がありますか。クライアントとサーバーのコードに呼び出しを挿入すると、Application Insights に独自の利用統計情報を送信できます。たとえば、注文を作成したが完了していないユーザーの数、最も頻繁に発生する検証エラー、またはゲームの平均スコアを確認できます。

[カスタム イベントおよびメトリックの API の詳細情報][track]。

## <a name="video"></a>ビデオ: 利用状況の追跡

> [AZURE.VIDEO tracking-usage-with-application-insights]

## <a name="next"></a>次のステップ

[カスタム イベントおよびメトリックを使用した利用状況の追跡][track]




[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]



