<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Application Insights に関するトラブルシューティングと Q & A" description="ヒントとトラブルシューティング" metaKeywords="analytics monitoring" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills" />
 
# トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights

+ [Visual Studio で Application Insights をプロジェクトに追加するオプションがありません](#q01)
+ [新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした](#q02)
+ [Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません](#q03)
+ [[利用状況分析] にデータが表示されません](#q04)
+ [Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?](#q05)
+ [自分のデータが表示される Azure のリソースを変更するにはどうすればいいですか?](#update)
+ [エラー「インストルメンテーション キーは空にできません」が発生しました](#emptykey)
+ [Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?](#q06)
+ [Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります](#q07)
+ [Application Insights を使ってイントラネット Web サーバーを監視できますか?](#q08)
+ [Windows Phone または Windows ストア用のデータはどのように入手できますか?](#q09)
+ [コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?](#q10)
+ [Application Insights に 2 つのバージョンがあるのはなぜですか?](#q11)
+ [Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?](#q13)
+ [Application Insights によってどのような変更がプロジェクトに加えられますか?](#q14)
+ [Application Insights で結果を見つけるにはどうすればいいですか?](#q15)
+ [ファイアウォールでどのようなポートを開く必要がありますか?](#q16)
+ [Application Insights の機能をすべて有効にしているでしょうか?](#q17)
+ [詳細情報](#next)



## <a name="q01"></a>Visual Studio で Application Insights をプロジェクトに追加するオプションがありません

+ Visual Studio Update 3[ を使用していることを確認してください。](http://go.microsoft.com/fwlink/?LinkId=397827)Visual Studio Update 3 にはプレインストールされた Application Insights Tools が付属します。このツールは、拡張機能マネージャーに表示されます。
+ 現在、Microsoft Azure プレビュー対応の Application Insights は、C# または Visual Basic で書かれた ASP.NET Web プロジェクトに対してのみ使用できます。
+ 既存のプロジェクトがある場合は、ソリューション エクスプローラーで、(別のプロジェクトまたはソリューションではなく) 対象の Web プロジェクトをクリックしていることを確認してください。[Application Insights テレメトリをプロジェクトに追加する] というメニュー項目があることを確認してください。
+ 新しいプロジェクトを作成している場合は、Visual Studio で、[ファイル]、[新しいプロジェクト]、[Visual C#] または [Visual Basic]、[Web]、[ASP.NET Web アプリケーション] の順に選択します。Application Insights をプロジェクトに追加するためのオプションがあることを確認してください。

## <a name="q02"></a>新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした

Application Insights ポータルとの通信に失敗した場合やアカウントに問題がある場合にこのような状況になることがあります。

+ 適切な Azure アカウントのログイン資格情報を指定していることを確認してください。[新しいプロジェクト] ダイアログで使用される Microsoft Azure 資格情報は、Visual Studio の右上に表示される Visual Studio Online 資格情報とは異なる場合があります。
+ しばらく待ってから [Application Insights を既存のプロジェクトに追加][start]してください。
+ Microsoft Azure アカウントの設定を表示し、制限の有無を確認してください。Application Insights アプリケーションを手動で追加できるかどうかを確認してください。


## <a name="q03"></a>Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません

+ データが表示されるブレードをいったん閉じてから開き直す必要があります。現在のバージョンでは、ブレードの内容は自動的に更新されません。
+ Microsoft Azure のスタート ボードで、サービス状態マップを確認してください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
+ ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。

## <a name="q04"></a>[利用状況分析] にデータが表示されません

+ データは、Web ページのスクリプトによって取得されます。Application Insights を既存の Web プロジェクトに追加した場合は、[スクリプトを手動で追加する][start]必要があります。


## <a name="q05"></a>Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?

次のどちらかの操作を行います。

* [参照]、[Application Insights]、目的のプロジェクト名の順に選択します。プロジェクトが表示されない場合は、[Visual Studio で Application Insights を Web プロジェクトに追加する][start]必要があります。

* Visual Studio のソリューション エクスプローラーで、Web プロジェクトを右クリックし、[Application Insights ポータルを開く] を選択します。

## <a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、**[Application Insights の更新]** を選択します。Azure の既存または新規のリソースにデータを送信できます。更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。[すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。

## <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました

Application Insights をインストールしているとき、またはログ アダプターをインストールしているときに、何かの問題が発生した可能性があります。

ソリューション エクスプローラーで、`ApplicationInsights.config` を右クリックし、**[Application Insights の更新]** を選択します。Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。

## <a name="q06"></a>Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?

いいえ。このマップには Azure サービスの状態が示されています。Web テストの結果を確認するには、[参照]、[Application Insights]、対象のアプリケーションの順に選択し、Web テストの結果を表示します。 


## <a name="q07"></a>Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります

おそらくお使いのバージョンは、Visual Studio Online バージョンに接続される [Application Insights Tools の以前のバージョン](http://msdn.microsoft.com/library/dn793604.aspx)です。

現在ご覧になっているヘルプ ページは、Visual Studio Update 3 に付属する、[Microsoft Azure プレビュー向けの Application Insights][start] を対象にしています。 

## <a name="q08"></a>Application Insights を使ってイントラネット Web サーバーを監視できますか?

はい。サーバーがデータをパブリック インターネットに送信できる場合は健全性と利用状況を監視できます。

ただし、サービスに対して Web テストを実行する場合は、パブリック インターネットからサービスにアクセスできる必要があります。

## <a name="q09"></a>Windows Phone または Windows ストア用のデータはどのように入手できますか?

Microsoft Azure バージョンでは、まだサポートされていません。[Visual Studio Online バージョン][older]を使用します。


## <a name="q10"></a>コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?

Microsoft Azure バージョンではこの機能はまだサポートされていません。近日対応予定です。現時点では、[以前のバージョン][older]をお試しください。


## <a name="q11"></a>Application Insights に 2 つのバージョンがあるのはなぜですか?

以前のポータルは、Visual Studio Online の一部です。今後、このバージョンには重要な変更は加えられません。Visual Studio 向け Application Insights Tools の以前のバージョンでは、Visual Studio Online ポータルに接続されます。

Visual Studio Update 3 には、プレインストールされた新しいバージョンの Application Insights Tools が付属します。新しいバージョンの Application Insights では、Microsoft Azure プレビューのコンポーネントである新しい Application Insights ポータルに接続されます。現在、Application Insights をこの新しい環境に移植する作業が行われています。作業はまだ完了していません。

## <a name="q13"></a>Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?

1. Visual Studio の拡張機能マネージャーを開きます。 
2. Application Insights ツールをアンインストールします。
3. [ツールの以前のバージョンのインストーラー](http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a)を実行し、「[get-started guide (開始方法のガイド)][older]」を参照します。

## <a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?

詳細は、プロジェクトの種類によって異なります。Web アプリケーションの場合: +


+ 次のファイルがプロジェクトに追加されます。

 + ApplicationInsights.config 
 + ai.js


+ 次の NuGet パッケージがインストールされます。

 -  *Application Insights API* - コア API

 -  *Application Insights API for Web Applications* - サーバーから利用統計情報を送信するために使用されます

 -  *Application Insights API for JavaScript Applications* - クライアントから利用統計情報を送信するために使用されます

    これらのパッケージには次のアセンブリが含まれています。

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ 次の項目を挿入します。

 - web.config

 - packages.config

+ (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合は、][start]手動でこの操作を行う必要があります)。スニペットをクライアントとサーバーのコードに追加して、Application Insights リソース ID で初期化します。たとえば、MVC アプリケーションでは、コードをマスター ページ Views/Shared/_Layout.cshtml に挿入します。


## <a name="q15"></a>Application Insights で結果を見つけるにはどうすればいいですか?
1. Microsoft Azure を開きます。
 - Visual Studio で、Web アプリケーション プロジェクトを右クリックし、**[Azure プレビュー ポータルを開く]** を選択します。
 - または、Web ブラウザーで、Microsoft Azure プレビューのアカウントを開きます。

2. [参照]、[Application Insights]、目的のプロジェクトの順に選択します。

## <a name="q16"></a>サーバーまたは開発用コンピューターとパブリック インターネットの間にファイアウォールが存在します。Application Insights を有効にするために、どのようなトラフィックを許可する必要がありますか。

パフォーマンスと利用状況データは、dc.services.visualstudio.com と f5.services.visualstudio.com の TCP ポート 80 および 443 に送信されます。

Web 可用性のテストは、ポート 80 で Web サーバーへの受信アクセスを使用します。

## <a name="q17"></a> Application Insights の機能をすべて有効にしているでしょうか?

<table border="1">
<tr><th>確認が必要な内容</th><th>取得する方法</th><th>取得する理由</th></tr>
<tr><td>可用性グラフ</td><td><a href="../app-insights-monitor-web-app-availability/">Web テスト</a></td><td>Web アプリケーションが稼働していることを確認する</td></tr>
<tr><td>サーバー アプリケーションのパフォーマンス: 応答時間など
</td><td><a href="../app-insights-start-monitoring-app-health-usage/">Application Insights をプロジェクトに追加する</a><br/>または<br/><a href="../app-insights-monitor-performance-live-website-now/">サーバーに AI 状態モニターをインストールする</a></td><td>パフォーマンスに関する問題を検出する</td></tr>
<!-- ####future#### <tr><td>依存関係の利用統計情報</td><td><a href="../app-insights-monitor-performance-live-website-now/">サーバーに AI 状態モニターをインストールする</a></td><td>データベースまたはその他の外部コンポーネントの問題を診断する</td></tr> -->
<!-- #####74.1#### <tr><td>サーバーのグローバル情報: CPU、メモリなど</td><td><a href="../app-insights-monitor-performance-live-website-now/">サーバーに AI 状態モニターをインストールする</a></td><td>処理能力の問題を診断する</td></tr> --> 
<tr><td>ログのトレースを検索する</td><td><a href="../app-insights-search-diagnostic-logs/">ログ アダプターを追加する</a></td><td>例外、パフォーマンスに関する問題を診断する</td></tr>
<tr><td>クライアントの利用状況の基本情報: ページのビュー、再訪数など</td><td><a href="../app-insights-start-monitoring-app-health-usage/#webclient">Web ページの JavaScript の初期化子</a></td><td>利用状況の分析</td></tr>
<tr><td>クライアントのカスタム メトリック</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">Web ページでの追跡呼び出し</a></td><td>ユーザー エクスペリエンスを強化する</td></tr>
<tr><td>サーバーのカスタム メトリック</td><td><a href="../app-insights-web-track-usage-custom-events-metrics/">サーバー コードでの追跡呼び出し</a></td><td>ビジネス インテリジェンス</td></tr>
</table>

Azure VM で Web サービスが実行されている場合は、そこでも[診断を取得][azurediagnostic]できます。



[AZURE.INCLUDE [app-insights-learn-more](../includes/app-insights-learn-more.md)]




[azurediagnostic]: ../insights-how-to-use-diagnostics/

[older]: http://www.visualstudio.com/get-started/get-usage-data-vs

