<properties 
    pageTitle="Windows デスクトップ アプリ用の Application Insights" 
    description="Application Insights を使用して Windows アプリの使用状況とパフォーマンスを分析します。" 
    services="application-insights" 
    documentationCenter="ios"
    authors="alancameronwills" 
    manager="ronmart"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2015" 
    ms.author="awills"/>

# iOS アプリ向けの Application Insights

Visual Studio の Application Insights を使用すると、使用状況、イベント、およびクラッシュに関してモバイル アプリケーションを監視できます。

## 必要条件

必要なものは次のとおりです。

* [Microsoft Azure](http://azure.com) のサブスクリプションWindows、XBox Live、またはその他の Microsoft クラウド サービスで所有している Microsoft アカウントを使用してサインインします。
* Xcode 6 以降。
* SDK は iOS 6.0 以降がインストールされているデバイスで実行されます。

## Application Insights リソースの作成

[Azure ポータル][portal]で、Application Insights の新しいリソースを作成します。iOS オプションを選択します。

![[新規]、[開発者向けサービス]、[Application Insights] の順にクリックする](./media/app-insights-ios/11-new.png)

表示されるブレードには、アプリに関するパフォーマンスと使用状況データが表示されます。次に Azure にログインするときにこのブレードに戻るには、スタート画面でそのタイルを見つけてください。あるいは、[参照] ボタンをクリックして探します。

## Mac 向けの Application Insights をダウンロードする

(まだインストールしていない場合。)

1. [Mac 向けの Application Insights](http://go.microsoft.com/fwlink/?LinkID=533209) をダウンロードします。

2. zip ファイルを解凍します。

3. アプリ アイコンをクリックし、Mac 向けの Application Insights を開始します。

## <a name="signin"></a>Azure にサインインする

1. [サインイン] をクリックします。

2. Azure アカウントでサインインします。

## アプリケーションに SDK をインストールする

1. [統合] をクリックし、SDK 統合を開始します。

2. 一覧から Xcode プロジェクトを選択するか、[他を開く] をクリックしてプロジェクトを探し、[統合] をクリックします。

3. Application Insights SDK のフォルダーを選択し、[インストール] をクリックします。

4. 表示された実行スクリプトをビルド フェーズに追加します。

    [実行スクリプト フェーズを追加します](http://hockeyapp.net/help/runscriptbuildphase/)。

5. 不足しているフレームワークを Xcode プロジェクトに追加します。

6. Xcode プロジェクトに Application Insights フレームワークをドラッグし、[次へ] をクリックします。

7. ターゲットの [ターゲットに SDK を統合] を選択します。

8. [コンポーネントの新規作成] をクリックし、Application Insights ポータルでアプリを作成します。

9. サブスクリプション、リソース グループを選択し、コンポーネントの名前を入力します。ほとんどの場合、これはアプリケーションの名前と一致する必要があります。[リソースの作成] ボタンで確定します。

10. 正しいコンポーネントが選択されていることを確認し、[次へ] をクリックします。

11. ウィザードの表示のようにソース コードを変更し、[完了] をクリックします。

12. [ビルドと実行] をクリックし、iOS シミュレーターでアプリを起動します。

## 利用統計情報呼び出しを挿入する

`[MSAIApplicationInsights start]` が呼び出されると、SDK はセッション、ページ ビュー、未処理の例外、クラッシュの追跡を開始します。

追加のイベントを次のように追加できます。

    // Send an event with custom properties and measuremnts data
    [MSAITelemetryManager trackEventWithName:@"Hello World event!"
                                  properties:@{@"Test property 1":@"Some value",
                                             @"Test property 2":@"Some other value"}
                                 measurements:@{@"Test measurement 1":@(4.8),
                                             @"Test measurement 2":@(15.16),
                                             @"Test measurement 3":@(23.42)}];

    // Send a message
    [MSAITelemetryManager trackTraceWithMessage:@"Test message"];

    // Manually send pageviews (note: this will also be done automatically)
    [MSAITelemetryManager trackPageView:@"MyViewController"
                               duration:300
                             properties:@{@"Test measurement 1":@(4.8)}];

    // Send custom metrics
    [MSAITelemetryManager trackMetricWithName:@"Test metric" 
                                        value:42.2];

## Application Insights でデータを表示する

http://portal.azure.com に戻り、Application Insights リソースを参照します。

[検索] をクリックして [[診断検索]][diagnostic] を開きます。ここには、最初のイベントが表示されます。何も表示されない場合は 1 ～ 2 分待機し、[更新] をクリックします。

![[診断検索] をクリックする](./media/app-insights-ios/21-search.png)

アプリケーションを使用すると、データが [概要] ブレードに表示されます。

![[概要] ブレード](./media/app-insights-ios/22-oview.png)

詳細情報を表示するグラフをクリックします。たとえば、クラッシュの場合は、次のようになります。

![クラッシュのグラフをクリックする](./media/app-insights-ios/23-crashes.png)
## <a name="usage"></a>次のステップ

[アプリの使用状況の追跡][track]

[診断検索][diagnostic]

[メトリックス エクスプローラー][metrics]

[トラブルシューティング][qna]


<!--Link references-->

[diagnostic]: app-insights-diagnostic-search.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[track]: app-insights-custom-events-metrics-api.md

<!---HONumber=58--> 