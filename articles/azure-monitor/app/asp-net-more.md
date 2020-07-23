---
title: Azure Application Insights をさらに活用する | Microsoft Docs
description: Application Insights の利用開始後に使用できる機能の概要を示します。
ms.topic: conceptual
ms.date: 02/03/2017
ms.openlocfilehash: 6d30833840b62412ae03c761baacf54c939c9d33
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86540028"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights からのテレメトリの追加
[Application Insights を ASP.NET コードに追加](../../azure-monitor/app/asp-net.md)した後、さらに多くのテレメトリを取得するためにできることがいくつかあります。 

| アクション | 取得内容|
|---|---|
|(IIS サーバー) 各サーバー コンピューターに [Status Monitor をインストール](https://go.microsoft.com/fwlink/?LinkId=506648)する。<br/>(Azure Web アプリ) Web アプリの Azure コントロール パネルで Application Insights ブレードを開く。| [**パフォーマンス カウンター**](../../azure-monitor/app/performance-counters.md)<br/>[**例外**](asp-net-exceptions.md) - 詳細なスタック トレース<br/>[**依存関係**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Web ページに JavaScript のスニペットを追加する](../../azure-monitor/app/javascript.md)|[ページのパフォーマンス](../../azure-monitor/app/usage-overview.md)、ブラウザーの例外、AJAX のパフォーマンス。 カスタム クライアント側テレメトリ。|
|[可用性 Web テストを作成する](../../azure-monitor/app/monitor-web-app-availability.md)|サイトが使用できなくなった場合、アラートを取得します|
|MSBuild によって [buildinfo.config が生成されることを確認する](/visualstudio/debugger/diagnose-problems-after-deployment?view=vs-2015)|[メトリック チャートで注釈を構築します](./annotations.md)
|[カスタム イベントとメトリックを作成する](../../azure-monitor/app/api-custom-events-metrics.md)|ビジネス イベントとメトリックのカウント、詳細な使用状況の追跡、その他を行います。|
|[ライブ サイトのプロファイリング](https://aka.ms/AIProfilerPreview)|ライブ Web アプリからの詳細な関数タイミング|
