<properties 
	pageTitle="Application Insights とは何か?" 
	description="ライブ Web またはデバイス アプリケーションの使用状況とパフォーマンスを追跡します。問題を検出し、優先順位を付け、診断します。継続的に監視し、ユーザーが満足するように改善します。" 
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
 
# Application Insights とは何か?

Visual Studio Application Insights を利用すれば、ライブ Web またはデバイス アプリケーションのパフォーマンスと使用状況を追跡できます。

* ほとんどのユーザーが気付く前にパフォーマンスの問題を[検出し、優先順位を付け、診断し][detect]、解決します。
 *  パフォーマンスの変化またはクラッシュに関するアラート。
 *  応答時間、CPU 使用率、依存関係追跡など、パフォーマンス問題の診断に役立つメトリック。
 *  Web アプリケーションの可用性テスト。
 *  クラッシュと例外のレポートとアラート。
 *  診断ログの高性能検索 (お気に入りのログ記録フレームワークのログ トレースを含む)。
* ユーザーがあなたのアプリケーションで何をするかということを理解し、[アプリケーションを継続的に改善します][knowUsers]。 
 * ページ ビューのカウント、新規ユーザーと再訪問ユーザー、その他の主要な使用状況分析。
 * 独自のイベントを追跡し、使用状況パターンと各機能の成功を評価します。

## それはどのように機能しますか?

小規模の SDK をアプリケーションにインストールし、Application Insights ポータルにアカウントを設定します。SDK はアプリを監視し、利用統計情報データをポータルに送信します。ポータルは統計グラフを表示し、問題の診断に役立つ高性能な検索ツールを提供します。

![アプリの Application Insights SDK は利用統計情報を Azure ポータルの Application Insights リソースに送信します。](./media/app-insights-overview/01-scheme.png)

SDK には、ユーザー、セッション、パフォーマンスをカウントする目的などで利用統計情報を集めるモジュールがいくつかあります。利用統計情報データをポータルに送信する独自のコードを記述することもできます。カスタムの利用統計情報はユーザー ストーリーを追跡する際に特に便利です。ボタンのクリック、特定の目標の達成、ユーザーのミスなどのイベントをカウントできます。

ASP.NET サーバーと Azure Web アプリについては、[Status Monitor][redfield] をインストールすることもできます。これには 2 とおりの使用方法があります。次の操作が可能になります。

* 再構築または再インストールすることなく、Web アプリを監視します。
* 依存関係モジュールの呼び出しを追跡します。

## どのような種類のアプリと連動しますか?

現在のところ、次のための SDK があります。

* Web アプリ
 * Azure または IIS サーバーの [ASP.NET][greenbrown]
 * JRE の [Java][java] 
 * [Web ページ][client]: HTML+JavaScript
* デバイス アプリ
 * [Windows][windows]
 * [iOS][ios]
 * [Android][android]
 * Cordova
 * [その他のプラットフォーム][platforms]


## 使用方法は?

* [問題の検出、トリアージ、診断][detect]
* [アプリの使用状況の分析][knowUsers]


## これを使用するには何が必要ですか?

* Microsoft Azure のサブスクリプションApplication Insights は、Web サイト、データベース、VM なども含まれている Azure のさまざまなクラウド サービスの 1 つです。(ただし、Application Insights を利用し、あらゆるアプリケーションを監視できます。アプリを Azure で実行する必要はありません。) 

 * あなたの会社にアカウントが与えられている場合があります。


## 開始するには?

左側の [開始] メニューからお使いのプラットフォームを選択します。

いずれのプラットフォームでも、基本的な手順は次のようになります。

1. [Azure][portal] で Application Insights リソースを作成します (それから、そのインストルメンテーション キーを取得します)。
2. 適切な SDK でアプリケーションをインストルメント化します (それから、インストルメンテーション キーで構成します)。
3. デバッグ モードまたはライブでアプリケーションを実行します。
4. [Azure][portal] でリソースの結果を確認します。

場合によっては、最初の 2 つの手順を自動で実行するプラグインを IDE に利用できます (Visual Studio や Eclipse など)。ただし、常に、手動で手順を進めることができます。

アプリが Web サイトまたはサービスの場合、基本的な手順にいくつかのオプションやバリエーションが追加されます。

* SDK をサーバー側のアプリケーションとクライアント [デバイス][windows]または [Web ページ][client]の両方に追加します。両方の端でイベントを相互に関連付けられるように、利用統計情報データがポータルで結合されます。
* 世界中の場所からサイトの可用性を監視する Web テストを設定します。
* 再構築または再展開せずに、既にライブになっているサーバー側のアプリケーションをインストルメント化します。これは [IIS サーバー][redfield]と [Azure Web アプリ][azure]で利用できます。
* データベースなど、他のコンポーネントに、あるいは REST API を介してアプリが行う依存関係呼び出しを監視します。[IIS サーバー][redfield]と [Azure Web アプリ][azure]で利用できます。


<!--Link references-->

[android]: app-insights-android.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[ios]: app-insights-ios.md
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[redfield]: app-insights-monitor-performance-live-website-now.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=62-->