<properties 
	pageTitle="Application Insights とは何か?" 
	description="ライブ Web またはデバイス アプリケーションの使用状況とパフォーマンスを追跡します。問題を検出し、優先順位を付け、診断します。継続的に監視し、ユーザーが満足するように改善します。" 
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
	ms.date="07/14/2015" 
	ms.author="awills"/>
 
# Application Insights とは何か?

Application Insights は拡張可能な分析サービスで、ライブ アプリケーションのパフォーマンスや使用状況を把握するのに役立ちます。開発者向けに設計された Application Insights を使用すると、アプリのパフォーマンスやユーザビリティを継続的に向上させることができます。

さまざまなプラットフォーム上の Web アプリやスタンドアロン アプリ (オンプレミスまたはクラウドでホストされた .NET または J2EE、Windows、iOS、Android、OSX、およびその他のプラットフォーム上のデバイス アプリなど) と連携します。

開発チームを支援することを目的としています。Application Insights では、次のことを行えます。

* [使用パターンの分析][knowUsers]。ユーザーを深く理解し、アプリを継続的に向上させます。 
 * ページ ビューのカウント、新規ユーザーと再訪問ユーザー、位置情報、プラットフォーム、その他の主要な使用状況分析
 * 使用状況のパスをトレースして、各機能の使用方法を評価します。
* ほとんどのユーザーが気付く前にパフォーマンスの問題を[検出し、優先順位を付け、診断し][detect]、解決します。
 *  パフォーマンスの変化またはクラッシュに関するアラート。
 *  応答時間、CPU 使用率、依存関係追跡など、パフォーマンス問題の診断に役立つメトリック。
 *  Web アプリケーションの可用性テスト。
 *  クラッシュと例外のレポートとアラート。
 *  診断ログの高性能検索 (お気に入りのログ記録フレームワークのログ トレースを含む)。

各プラットフォーム用の SDK には、そのまま使用できるアプリを監視するさまざまなモジュールが用意されています。さらに、調整されたより詳しい分析のために、独自のテレメトリをコーディングすることもできます。

アプリケーションから収集されたテレメトリ データは Azure ポータルに格納、分析されます。ここでは、直感的なビューやパワフルなツールにより迅速な分析や診断を行えます。

![ユーザー アクティビティの統計をグラフ化したり、特定のイベントをドリルダウンしたりすることができます。](./media/app-insights-overview/00-sample.png)

詳細な分析が必要? データを [SQL](app-insights-code-sample-export-telemetry-sql-database.md)、[Power BI](app-insights-export-power-bi.md)、または独自のツールに[エクスポート](app-insights-export-telemetry.md)します。

![Power BI でのデータ表示](./media/app-insights-overview/210.png)

## プラットフォームと言語

SDK は増え続けるプラットフォームに対応します。最新の一覧を次に示します。

 * [ASP.NET サーバー][greenbrown] (Azure または IIS サーバー上)
 * [Azure Cloud Services](app-insights-cloudservices.md)
 * [J2EE サーバー][java]
 * [Web ページ][client]\: HTML+JavaScript
 * [Windows Phone、Windows Store、Windows 10 ユニバーサル アプリのほか、Windows 10 開発者向けポータルに直接統合できます][windows]
 * [Windows デスクトップ][desktop]
 * [iOS][ios]
 * [Android][android]
 * [他のプラットフォーム][platforms] - Node.js、PHP、Python、Ruby、Joomla、SharePoint、WordPress

Application Insights では、再構築せずに、IIS 上の既存の ASP.NET Web アプリからテレメトリを取得することもできます。

アプリにクライアント、サーバー、およびその他のコンポーネントがある場合、それらすべてをインストルメント化できます。たとえば、データを Application Insights ポータルに組み込んで、クライアントのイベントをサーバーのイベントと関連付けることができます。


## 動作のしくみ

小規模の SDK をアプリケーションにインストールし、Application Insights ポータルにアカウントを設定します。SDK はアプリを監視し、利用統計情報データをポータルに送信します。ポータルは統計グラフを表示し、問題の診断に役立つ高性能な検索ツールを提供します。

![アプリの Application Insights SDK は利用統計情報を Azure ポータルの Application Insights リソースに送信します。](./media/app-insights-overview/01-scheme.png)

SDK には、ユーザー、セッション、パフォーマンスをカウントする目的などで利用統計情報を集めるモジュールがいくつかあります。利用統計情報データをポータルに送信する独自のコードを記述することもできます。カスタムの利用統計情報はユーザー ストーリーを追跡する際に特に便利です。ボタンのクリック、特定の目標の達成、ユーザーのミスなどのイベントをカウントできます。

ASP.NET サーバーと Azure Web アプリについては、[Status Monitor][redfield] をインストールすることもできます。これには 2 とおりの使用方法があります。次の操作が可能になります。

* 再構築または再インストールすることなく、Web アプリを監視します。
* 依存関係モジュールの呼び出しを追跡します。



### オーバーヘッドとは

パフォーマンスへの影響はごく小さいものです。追跡は非ブロッキングを呼び出し、バッチ処理されて、個別のスレッドに送信されます。



## 作業を開始するには:

1. 使用を開始するには、[Microsoft Azure](http://azure.com) のサブスクリプションが必要です。サインアップは無料で、Application Insights の無料の[価格レベル](https://azure.microsoft.com/pricing/details/application-insights/)を選択できます。

2. [Azure プレビュー ポータル](http://portal.azure.com)にサインインする
3. Application Insights リソースを作成します。アプリのデータはここに表示されます。

    ![[追加]、[開発者向けサービス]、[Application Insights]](./media/app-insights-overview/11-new.png)

    アプリケーションの種類を選択します。

4. 新しいリソースを開き、クイック スタート ガイドを開きます。
    
    ![参照](./media/app-insights-overview/quickstart.png)

    これは、アプリで SDK をインストールする方法について説明します。Web アプリについては、SDK を Web ページに追加したり、可用性テストを設定する方法についても記載されています。


詳細については、このページの左側のナビゲーション バーの [開始] でアプリの種類を選択してください。


## サポートとフィードバック

* 質問および問題点:
 * [トラブルシューティング][qna]
 * [MSDN フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
 * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
* バグ:
 * [マイクロソフトに報告する](https://connect.microsoft.com/VisualStudio/Feedback/LoadSubmitFeedbackForm?FormID=6076)
* ご提案:
 * [ユーザーの声](http://visualstudio.uservoice.com/forums/121579-visual-studio/category/77108-application-insights)


## ビデオ


> [AZURE.VIDEO 218]

> [AZURE.VIDEO usage-monitoring-application-insights]

> [AZURE.VIDEO performance-monitoring-application-insights]


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=July15_HO5-->