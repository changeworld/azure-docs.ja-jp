---
title: Azure Status Monitor v2 概要 | Microsoft Docs
description: Status Monitor v2 の概要。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 2adc706c5da4fa53ace2a8a471789e276878c491
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255855"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

Status Monitor v2 は、[PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) に公開された PowerShell モジュールであり、[Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) を置き換えるものです。 このモジュールは、IIS でホストされている .NET Web アプリをコードの記述なしにインストルメンテーションできるようにします。
テレメトリが Azure portal に送信され、そこでアプリケーションを[監視](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)できます。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="powershell-gallery"></a>PowerShell ギャラリー

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instructions
- 簡単なコード サンプルで使用を開始するには、[概要の説明](status-monitor-v2-get-started.md)をご覧ください。
- 開始する方法の詳細については、[詳細な手順](status-monitor-v2-detailed-instructions.md)をご覧ください。

## <a name="powershell-api-reference"></a>PowerShell API リファレンス
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>トラブルシューティング
- [トラブルシューティング](status-monitor-v2-troubleshoot.md)
- [既知の問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>FAQ

- Status Monitor v2 は、プロキシ インストールをサポートしますか?

  **はい**。 Status Monitor v2 をダウンロードする複数のオプションがあります。 コンピューターがインターネットにアクセスできる場合は、`-Proxy` パラメーターを使用して PowerShell ギャラリーにオンボードできます。 このモジュールを手動でダウンロードして、マシンにインストールするかまたはモジュールを直接使用することもできます。 これらの各オプションについては、[詳細な手順](status-monitor-v2-detailed-instructions.md)で説明しています。
  
- 有効化が成功したことをどうやって確認すればよいですか?

   有効化が成功したことを確認するコマンドレットはありません。 [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) を使用して、アプリケーションがテレメトリを送信していることを確認することをお勧めします。

   [Analytics](../log-query/get-started-portal.md) を使用して、現在テレメトリを送信しているすべてのクラウド ロールを一覧表示することもできます。
   ```Kusto
   union * | summarize count() by cloud_RoleName, cloud_RoleInstance
   ```

## <a name="next-steps"></a>次の手順

テレメトリの表示:

* パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します
* 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します
* より高度なクエリのために [Analytics](../../azure-monitor/app/analytics.md) を使用します
* [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します

テレメトリの追加:

* サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
* Web ページ コードからの例外を参照してトレースの呼び出しを挿入するために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
* トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。

