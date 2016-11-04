---
title: Application Insights を使用した ASP.NET の Web アプリ分析を設定する | Microsoft Docs
description: オンプレミスまたは Azure でホストされている ASP.NET Web サイトのパフォーマンス、可用性、利用状況の分析を構成します。
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: douge

ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/09/2016
ms.author: daviste

---
# ASP.NET 向けの Application Insights の設定
[Visual Studio Application Insights](app-insights-overview.md) は、実行中のアプリケーションを監視し、[パフォーマンスの問題や例外の検出と診断](app-insights-detect-triage-diagnose.md)、[アプリの使用方法の把握](app-insights-overview-usage.md)に役立ちます。Azure Web アプリに加えて、独自のオンプレミス IIS サーバーやクラウドの仮想マシンでホストされているアプリに対しても機能します。

## 開始する前に
必要なもの:

* Visual Studio 2013 Update 3 以降。より新しいバージョンが適しています。
* [Microsoft Azure](http://azure.com) のサブスクリプションチームまたは組織で Azure サブスクリプションを取得している場合、所有者は [Microsoft アカウント](http://live.com)を使用してあなたを追加できます。

関心をお持ちの場合は、他にも次のような記事をご覧いただけます。

* [実行時の Web アプリのインストルメント化](app-insights-monitor-performance-live-website-now.md)
* [Azure Cloud Services](app-insights-cloudservices.md)

## <a name="ide"></a> 1.Application Insights SDK の追加
### 新しいプロジェクトの場合
Visual Studio で新しいプロジェクトを作成するときに、Application Insights が選択されていることを確認してください。

![ASP.NET プロジェクトを作成する](./media/app-insights-asp-net/appinsights-01-vsnewp1.png)

### 既存のプロジェクトの場合
ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights テレメトリの追加]** または **[Application Insights の構成]** を選択します。

![[Application Insights の追加] を選択する](./media/app-insights-asp-net/appinsights-03-addExisting.png)

* ASP.NET Core プロジェクトの場合 - [これらの手順に従って、コード行をいくつか修正します](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started#add-application-insights-instrumentation-code-to-startupcs)。

## <a name="run"></a> 2.アプリケーションを実行する
F5 キーを使用してアプリケーションを実行して、試します。さまざまなページが開き、いくつかのテレメトリが生成されます。

Visual Studio で、ログに記録されたイベントの数が表示されます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/54.png)

## 3\.テレメトリの確認
### Visual Studio で確認する
[Application Insights] ボタンをクリックするか、次のようにソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights] ウィンドウを開きます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/app-insights-asp-net/55.png)

このビューには、アプリのサーバー側で生成されたテレメトリが表示されます。フィルターを試したり、任意のイベントをクリックして詳細を表示したりしてみましょう。

[Visual Studio の Application Insights ツールの詳細については、こちらを参照してください](app-insights-visual-studio.md)。

<a name="monitor"></a>

### ポータルで確認する
*[SDK のみをインストール]* を選択した場合を除き、Application Insights Web ポータルでもテレメトリを確認できます。

ポータルには、Visual Studio より多くのグラフ、分析ツール、ダッシュボードが用意されています。

[Azure ポータル](https://portal.azure.com/)に Application Insights のリソースを開きます。

![プロジェクトを右クリックして Azure ポータルを開く](./media/app-insights-asp-net/appinsights-04-openPortal.png)

ポータルを開くと、アプリのテレメトリが表示されます。 ![](./media/app-insights-asp-net/66.png)

* [ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream)に最初のテレメトリが表示されます。
* **[検索]** (1) に個々のイベントが表示されます。データが表示されるまでに数分かかることがあります。イベントのプロパティを表示するには、イベントをクリックします。
* メトリックの集計値はグラフ (2) に表示されます。データがここに表示されるまで、1 ～ 2 分かかる場合があります。グラフをクリックすると、詳細を含むブレードが開きます。

[Azure ポータルでの Application Insights の使用方法の詳細については、こちらを参照してください](app-insights-dashboards.md)。

## 4\.アプリケーションの発行
IIS サーバーまたは Azure にアプリを発行します。[ライブ メトリック ストリーム](app-insights-metrics-explorer.md#live-metrics-stream)を観察して、必要な処理がすべて滞りなく実行されていることを確認してください。

Application Insights ポータルにはテレメトリが蓄積されており、メトリックを監視したり、目的のテレメトリを検索したり、[ダッシュボード](app-insights-dashboards.md)を設定したりすることができます。強力な [Analytics クエリ言語](app-insights-analytics.md)を使って使用状況やパフォーマンスを分析したり、特定のイベントを見つけたりすることができます。

[Visual Studio](app-insights-visual-studio.md) から各種ツール (診断検索、[傾向](app-insights-visual-studio-trends.md)など) を使って継続的にテレメトリを分析することもできます。

> [!NOTE]
> アプリから送信されたテレメトリの量が[スロットル制限](app-insights-pricing.md#limits-summary)に近づくと、自動[サンプリング](app-insights-sampling.md)がオンに変わります。アプリから送信されるテレメトリの量をサンプリングによって抑えながら、診断に利用できる相関性のあるデータを維持することができます。
> 
> 

## <a name="land"></a> [Application Insights の追加] の実行結果
Application Insights によって、アプリのテレメトリが (Microsoft Azure でホストされている) Application Insights ポータルに送信されます。

![](./media/app-insights-asp-net/01-scheme.png)

つまり、コマンドによって実行された操作は次の 3 つです。

1. プロジェクトに Application Insights Web SDK NuGet パッケージを追加する。Visual Studio で表示するには、プロジェクトを右クリックし、[NuGet パッケージの管理] を選択します。
2. [Azure ポータル](https://portal.azure.com/)で、Application Insights のリソースを作成する。ここにデータが表示されます。リソースを識別する*インストルメンテーション キー*を取得します。
3. `ApplicationInsights.config` にインストルメンテーション キーを挿入し、SDK がポータルにテレメトリを送信できるようにする。

必要な場合は、[ASP.NET 4](app-insights-windows-services.md) または [ASP.NET Core](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started) 向けにこれらの手順を手動で実行することもできます。

### 新しいバージョンの SDK にアップグレードするには
[SDK の新しいリリース](https://github.com/Microsoft/ApplicationInsights-dotnet-server/releases)にアップグレードするには、NuGet パッケージ マネージャーをもう一度開き、インストールされているパッケージに対してフィルターを実行します。[Microsoft.ApplicationInsights.Web]、[アップグレード] の順に選択します。

ApplicationInsights.config をカスタマイズしている場合は、アップグレードする前にコピーを保存しておき、後から新しいバージョンに変更をマージします。

## 次のステップ
|  |
| --- | --- |
| **[Visual Studio で Application Insights を使用する](app-insights-visual-studio.md)**<br/>テレメトリを使用したデバッグ、診断検索、コードのドリル スルー。 |
| **[Application Insights ポータルの操作](app-insights-dashboards.md)**<br/>ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、テレメトリのエクスポート。 |
| **[データの追加](app-insights-asp-net-more.md)**<br/>使用状況、可用性、依存関係、例外の監視。ログ記録フレームワークからのトレースを統合します。カスタム テレメトリを記述します。 |

<!---HONumber=AcomDC_0907_2016-->