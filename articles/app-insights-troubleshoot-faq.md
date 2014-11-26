<properties title="Troubleshooting and Q & A about Application Insights" pageTitle="Troubleshooting and Q & A about Application Insights" description="Tips and troubleshooting" metaKeywords="analytics monitoring" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights

-   [Visual Studio で Application Insights をプロジェクトに追加するオプションがありません][Visual Studio で Application Insights をプロジェクトに追加するオプションがありません]
-   [新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした][新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした]
-   [Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません][Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません]
-   [[利用状況分析] にデータが表示されません](#q04)
-   [Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?][Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?]
-   [Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?][Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?]
-   [Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります][Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります]
-   [Application Insights を使ってイントラネット Web サーバーを監視できますか?][Application Insights を使ってイントラネット Web サーバーを監視できますか?]
-   [Windows Phone または Windows ストア用のデータはどのように入手できますか?][Windows Phone または Windows ストア用のデータはどのように入手できますか?]
-   [コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?][コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?]
-   [Application Insights に 2 つのバージョンがあるのはなぜですか?][Application Insights に 2 つのバージョンがあるのはなぜですか?]
-   [Application Insights の Azure バージョンにない機能にはどのような機能がありますか?][Application Insights の Azure バージョンにない機能にはどのような機能がありますか?]
-   [Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?][Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?]
-   [Application Insights によってどのような変更がプロジェクトに加えられますか?][Application Insights によってどのような変更がプロジェクトに加えられますか?]
-   [Application Insights で結果を見つけるにはどうすればいいですか?][Application Insights で結果を見つけるにはどうすればいいですか?]
-   [次のステップ][次のステップ]

## <a name="q01"></a>Visual Studio で Application Insights をプロジェクトに追加するオプションがありません

-   [Visual Studio Update 3][Visual Studio Update 3] を使用していることを確認してください。Visual Studio Update 3 にはプレインストールされた Application Insights Tools が付属します。このツールは、拡張機能マネージャーに表示されます。
-   現在、Microsoft Azure プレビュー対応の Application Insights は、C# または Visual Basic で書かれた ASP.NET Web プロジェクトに対してのみ使用できます。
-   既存のプロジェクトがある場合は、ソリューション エクスプローラーで、(別のプロジェクトまたはソリューションではなく) 対象の Web プロジェクトをクリックしていることを確認してください。[Application Insights テレメトリをプロジェクトに追加する] というメニュー項目があることを確認してください。
-   新しいプロジェクトを作成している場合は、Visual Studio で、[ファイル]、[新しいプロジェクト]、[Visual C#] または [Visual Basic]、[Web]、[ASP.NET Web アプリケーション] の順に選択します。Application Insights をプロジェクトに追加するためのオプションがあることを確認してください。

## <a name="q02"></a>新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした

Application Insights ポータルとの通信に失敗した場合やアカウントに問題がある場合にこのような状況になることがあります。

-   適切な Azure アカウントのログイン資格情報を指定していることを確認してください。[新しいプロジェクト] ダイアログで使用される Microsoft Azure 資格情報は、Visual Studio の右上に表示される Visual Studio Online 資格情報とは異なる場合があります。
-   しばらく待ってから [Application Insights を既存のプロジェクトに追加][Application Insights を既存のプロジェクトに追加]してください。
-   Microsoft Azure アカウントの設定を表示し、制限の有無を確認してください。Application Insights アプリケーションを手動で追加できるかどうかを確認してください。

## <a name="q03"></a>Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません

-   データが表示されるブレードをいったん閉じてから開き直す必要があります。現在のバージョンでは、ブレードの内容は自動的に更新されません。
-   Microsoft Azure のスタート ボードで、サービス状態マップを確認してください。アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
-   Web サーバーのファイアウォールで、ポート 443 を発信トラフィック用に開く必要があります。

## <a name="q04"></a>[利用状況分析] にデータが表示されません

-   データは、Web ページのスクリプトによって取得されます。Application Insights を既存の Web プロジェクトに追加した場合は、[スクリプトを手動で追加する][Application Insights を既存のプロジェクトに追加]必要があります。

## <a name="q05"></a>Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?

次のどちらかの操作を行います。

-   [参照]、[Application Insights]、目的のプロジェクトの順に選択します。プロジェクトが表示されない場合は、[Visual Studio で Application Insights を Web プロジェクトに追加する][Application Insights を既存のプロジェクトに追加]必要があります。

-   Visual Studio のソリューション エクスプローラーで、Web プロジェクトを右クリックし、[Application Insights ポータルを開く] を選択します。

## <a name="q06"></a>Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?

いいえ。このマップには Azure サービスの状態が示されています。Web テストの結果を確認するには、[参照]、[Application Insights]、対象のアプリケーションの順に選択し、Web テストの結果を表示します。

## <a name="q07"></a>Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります

おそらくお使いのバージョンは、Visual Studio Online バージョンに接続される Application Insights Tools の以前のバージョンです。

現在ご覧になっているヘルプ ページは、Visual Studio Update 3 に付属する、Microsoft Azure プレビュー向けの Application Insights を対象にしています。

## <a name="q08"></a>Application Insights を使ってイントラネット Web サーバーを監視できますか?

はい。サーバーがデータをパブリック インターネットに送信できる場合は健全性と利用状況を監視できます。

ただし、サービスに対して Web テストを実行する場合は、パブリック インターネットからサービスにアクセスできる必要があります。

## <a name="q10"></a>コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?

Microsoft Azure バージョンではこの機能はまだサポートされていません。近日対応予定です。現時点では、[以前のバージョン][以前のバージョン]をお試しください。

## <a name="q11"></a>Application Insights に 2 つのバージョンがあるのはなぜですか?

以前のポータルは、Visual Studio Online の一部です。今後、このバージョンには重要な変更は加えられません。Visual Studio 向け Application Insights Tools の以前のバージョンでは、Visual Studio Online ポータルに接続されます。

Visual Studio Update 3 には、プレインストールされた新しいバージョンの Application Insights Tools が付属します。新しいバージョンの Application Insights では、Microsoft Azure プレビューのコンポーネントである新しい Application Insights ポータルに接続されます。現在、Application Insights をこの新しい環境に移植する作業が行われています。移植作業はまだ完了しておらず、いくつかの制限があります。

## <a name="q12"></a>Application Insights の Azure バージョンにない機能にはどのような機能がありますか?

この大がかりな作業はまだ途中の段階にあります。

現時点で実装されていない主な機能には、デバイス アプリケーション (Windows Phone、Windows ストアなど) のサポートと、カスタム テレメトリ (`trackEvent()`、`trackPageView()` など) のレポートがあります。

## <a name="q13"></a>Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?

1.  Visual Studio の拡張機能マネージャーを開きます。
2.  Application Insights ツールをアンインストールします。
3.  [ツールの以前のバージョンのインストーラー][ツールの以前のバージョンのインストーラー]を実行し、[入門ガイド][以前のバージョン]を読みます。

## <a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?

-   次のファイルがプロジェクトに追加されます。

 + ApplicationInsights.config. 
 + ai.js



-  次の NuGet パッケージがインストールされます。

 + Application Insights API* - コア API

 + Application Insights API for Web Applications* - サーバーからテレメトリを送信するために使用されます

 + Application Insights API for JavaScript Applications* - クライアントからテレメトリを送信するために使用されます

    これらのパッケージには次のアセンブリが含まれています。

 + Microsoft.ApplicationInsights

 + Microsoft.ApplicationInsights.Platform

-   (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][Application Insights を既存のプロジェクトに追加]は、手動でこの操作を行う必要があります)。スニペットをクライアントとサーバーのコードに追加して、Application Insights リソース ID で初期化します。たとえば、MVC アプリケーションでは、コードを次のファイルに挿入します。

 + マスター ページの Views/Shared/\_Layout.cshtml
 + web.config

 + packages.config

## <a name="q15"></a>Application Insights で結果を見つけるにはどうすればいいですか?
1.  Microsoft Azure を開きます。
 + Visual Studio で、Web アプリケーション プロジェクトを右クリックし、**[Azure プレビュー ポータルを開く]** を選択します。
 + または、Web ブラウザーで、Microsoft Azure プレビューのアカウントを開きます。

1.  [参照]、[Application Insights]、目的のプロジェクトの順に選択します。

## <a name="next"></a>詳細情報

-   [Application Insights - 概要][Application Insights - 概要]
-   [アプリケーションの正常性と利用状況の監視][Application Insights を既存のプロジェクトに追加]
-   [ライブ Web サーバーの監視を今すぐ始める][ライブ Web サーバーの監視を今すぐ始める]
-   [メトリックの探索][メトリックの探索]
-   [Application Insights での診断ログの検索][Application Insights での診断ログの検索]
-   [Web アプリケーションの可用性と応答性の監視][Web アプリケーションの可用性と応答性の監視]
-   [カスタム イベントおよびメトリックを使用した利用状況の追跡][カスタム イベントおよびメトリックを使用した利用状況の追跡]
-   [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights][トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights]

<!--Link references-->

  [Visual Studio で Application Insights をプロジェクトに追加するオプションがありません]: #q01
  [新しい Web プロジェクトが作成されましたが Application Insights が追加されませんでした]: #q02
  [Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません]: #q03
  [Microsoft Azure プレビューのスタート ボードを表示しています。Application Insights でデータを見つけるにはどうすればいいですか?]: #q05
  [Microsoft Azure プレビューのホーム画面のマップに表示されているのはアプリケーションの状態ですか?]: #q06
  [Application Insights をアプリケーションに追加する機能を使用して Application Insights ポータルを開いたときの画面が、説明に含まれているスクリーンショットとまったく異なります]: #q07
  [Application Insights を使ってイントラネット Web サーバーを監視できますか?]: #q08
  [Windows Phone または Windows ストア用のデータはどのように入手できますか?]: #q09
  [コードでログ記録したイベントやページ ビューを表示するにはどうすればいいですか?]: #q10
  [Application Insights に 2 つのバージョンがあるのはなぜですか?]: #q11
  [Application Insights の Azure バージョンにない機能にはどのような機能がありますか?]: #q12
  [Application Insights の Visual Studio Online バージョンにあったすべての機能を再び利用するにはどうすればいいですか?]: #q13
  [Application Insights によってどのような変更がプロジェクトに加えられますか?]: #q14
  [Application Insights で結果を見つけるにはどうすればいいですか?]: #q15
  [次のステップ]: #next
  [Visual Studio Update 3]: http://go.microsoft.com/fwlink/?LinkId=397827
  [Application Insights を既存のプロジェクトに追加]: ../app-insights-monitor-application-health-usage/
  [以前のバージョン]: http://www.visualstudio.com/get-started/get-usage-data-vs
  [ツールの以前のバージョンのインストーラー]: http://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a
  [Application Insights - 概要]: ../app-insights-get-started/
  [ライブ Web サーバーの監視を今すぐ始める]: ../app-insights-monitor-performance-live-website-now/
  [メトリックの探索]: ../app-insights-explore-metrics/
  [Application Insights での診断ログの検索]: ../app-insights-search-diagnostic-logs/
  [Web アプリケーションの可用性と応答性の監視]: ../app-insights-monitor-web-app-availability/
  [カスタム イベントおよびメトリックを使用した利用状況の追跡]: ../app-insights-track-usage-custom-events-metrics/
  [トラブルシューティングおよび Q & A - Microsoft Azure プレビューの Application Insights]: ../app-insights-troubleshoot-faq/
