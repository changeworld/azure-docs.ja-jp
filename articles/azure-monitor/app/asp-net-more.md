---
title: Azure Application Insights をさらに活用する | Microsoft Docs
description: Application Insights の利用開始後に使用できる機能の概要を示します。
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 7ec10a2d-c669-448d-8d45-b486ee32c8db
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 02/03/2017
ms.author: mbullwin
ms.openlocfilehash: 7e62efb0b64b0b882b36126af4a1f9d520d92475
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65230643"
---
# <a name="more-telemetry-from-application-insights"></a>Application Insights からのテレメトリの追加
[Application Insights を ASP.NET コードに追加](../../azure-monitor/app/asp-net.md)した後、さらに多くのテレメトリを取得するためにできることがいくつかあります。 

| [操作] | 取得内容|
|---|---|
|(IIS サーバー) 各サーバー コンピューターに [Status Monitor をインストール](https://go.microsoft.com/fwlink/?LinkId=506648)する。<br/>(Azure Web アプリ) Web アプリの Azure コントロール パネルで Application Insights ブレードを開く。| [**パフォーマンス カウンター**](../../azure-monitor/app/performance-counters.md)<br/>[**例外**](asp-net-exceptions.md) - 詳細なスタック トレース<br/>[**依存関係**](../../azure-monitor/app/asp-net-dependencies.md)|
|[Web ページに JavaScript のスニペットを追加する](../../azure-monitor/app/javascript.md)|[ページのパフォーマンス](../../azure-monitor/app/usage-overview.md)、ブラウザーの例外、AJAX のパフォーマンス。 カスタム クライアント側テレメトリ。|
|[可用性 Web テストを作成する](../../azure-monitor/app/monitor-web-app-availability.md)|サイトが使用できなくなった場合、アラートを取得します|
|MSBuild によって [buildinfo.config が生成されることを確認する](https://msdn.microsoft.com/library/dn449058.aspx)|[メトリック チャートで注釈を構築します](https://docs.microsoft.com/azure/azure-monitor/app/annotations)
|[カスタム イベントとメトリックを作成する](../../azure-monitor/app/api-custom-events-metrics.md)|ビジネス イベントとメトリックのカウント、詳細な使用状況の追跡、その他を行います。|
|[ライブ サイトのプロファイリング](https://aka.ms/AIProfilerPreview)|ライブ Web アプリからの詳細な関数タイミング|






