---
title: "Visual Studio での Application Insights の操作"
description: "デバッグ中および運用環境でのパフォーマンス分析と診断。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 2059802b-1131-477e-a7b4-5f70fb53f974
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/21/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 2334a85402049eb8654039e98cf2c3131a43de62


---
# <a name="working-with-application-insights-in-visual-studio"></a>Visual Studio での Application Insights の操作
Visual Studio (2015 以降) では、[Azure Application Insights](app-insights-overview.md)からのテレメトリを使用し、パフォーマンスの分析と問題の診断を、デバッグ中と運用環境の両方において実行できます。

[アプリに Application Insights をインストール](app-insights-asp-net.md)していない場合は、今すぐインストールしてください。

## <a name="a-nameruna-debug-your-project"></a><a name="run"></a> プロジェクトのデバッグ
F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、ログに記録されたイベント数が表示されます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-visual-studio/appinsights-09eventcount.png)

このボタンをクリックして診断検索を開きます。 

## <a name="diagnostic-search"></a>診断検索
[検索] ウィンドウには、ログに記録されたイベントが表示されます  (Application Insights を設定する際に Azure にサインインした場合は、ポータルで同じイベントを検索できるようになります)。

![プロジェクトを右クリックし、[Application Insights]、[検索] を選択する](./media/app-insights-visual-studio/34.png)

フリー テキスト検索は、イベント内の任意のフィールドに使用できます。 たとえば、ページの URL の一部や、クライアントの市区町村などのプロパティ値、トレース ログの特定の単語などを検索できます。

イベントをクリックすると、その詳細なプロパティが表示されます。

[関連アイテム] タブを開いて、失敗した要求や例外を診断することもできます。

![](./media/app-insights-visual-studio/41.png)

## <a name="diagnostics-hub"></a>診断ハブ
Application Insights サーバー テレメトリが生成されると、診断ハブ (Visual Studio 2015 以降) に表示されます。 これは、SDK のインストールだけを選択した場合でも、Azure ポータルでリソースに接続しなくても機能します。

![診断ツール ウィンドウを開き、Application Insights のイベントを調べます。](./media/app-insights-visual-studio/31.png)

## <a name="exceptions"></a>例外
[例外の監視を設定](app-insights-asp-net-exceptions.md)している場合は、例外レポートが [検索] ウィンドウに表示されます。 

スタック トレースを取得するには、例外をクリックします。 Visual Studio でアプリのコードが開かれている場合は、コードの該当する行をスタック トレースからクリックできます。

![Exception stack trace](./media/app-insights-visual-studio/17.png)

さらに、各メソッドの上にある CodeLens 行には、Application Insights によって記録された過去 24 時間の例外の数が表示されます。

![Exception stack trace](./media/app-insights-visual-studio/21.png)

## <a name="local-monitoring"></a>ローカル監視
Visual Studio 2015 Update 2 以降、Application Insights ポータルにテレメトリを送信するように SDK を構成していない (ApplicationInsights.config にインストルメンテーション キーが存在しない) 場合、診断ウィンドウには、直近のデバッグ セッションからテレメトリが表示されます。 

これは以前のバージョンのアプリを既に発行済みである場合に役立ちます。 デバッグ セッションから得られたテレメトリが、Application Insights ポータル上の発行済みアプリから得られたテレメトリと混同されるのは望ましくありません。

これは、ポータルにテレメトリを送信する前に、いくつかの [カスタム テレメトリ](app-insights-api-custom-events-metrics.md) をデバッグする場合にも役立ちます。

* *最初は、ポータルにテレメトリを送信するよう Application Insights を構成しましたが、今は、Visual Studio でテレメトリだけを表示したくなりました。*
  
  * アプリからポータルにテレメトリを送信している場合でも、[検索] ウィンドウの [設定] に用意されているオプションでローカルの診断を検索できます。
  * ポータルへのテレメトリの送信を中止するには、ApplicationInsights.config から `<instrumentationkey>...` 行をコメント アウトしてください。 もう一度ポータルにテレメトリを送信する準備ができたら、コメント解除します。

## <a name="trends"></a>傾向
傾向とは、時間経過に伴うアプリの動作を視覚化するためのツールです。 

Application Insights のツール バー ボタンか [Application Insights の検索] ウィンドウから、 **[テレメトリの傾向を調べる]** を選択します。 5 つの一般的なクエリから 1 つ選択して開始します。 テレメトリの種類、時間範囲、およびその他のプロパティに基づき、さまざまなデータセットを分析できます。 

データ内の異常を見つけるには、[ビューの種類] ボックスでいずれかの異常オプションを選択します。 ウィンドウの下部にあるフィルター オプションを使用すると、テレメトリの特定の部分に対象を絞り込みやすくなります。

![傾向](./media/app-insights-visual-studio/51.png)

傾向について詳しくは、[こちら](app-insights-visual-studio-trends.md)を参照してください。

## <a name="whats-next"></a>次の手順
|  |  |
| --- | --- |
| **[データの追加](app-insights-asp-net-more.md)**<br/>使用状況、可用性、依存関係、例外の監視。 ログ記録フレームワークからのトレースを統合します。 カスタム テレメトリを記述します。 |![Visual studio](./media/app-insights-visual-studio/64.png) |
| **[Application Insights ポータルの操作](app-insights-dashboards.md)**<br/>ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポート。 |![Visual studio](./media/app-insights-visual-studio/62.png) |




<!--HONumber=Nov16_HO3-->


