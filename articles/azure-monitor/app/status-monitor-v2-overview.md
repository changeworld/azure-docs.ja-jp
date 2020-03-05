---
title: Azure Application Insights エージェントの概要 | Microsoft Docs
description: Application Insights エージェントの概要。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 09/16/2019
ms.openlocfilehash: 4a240bc62816a46bc37108777a8b081b74047738
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671173"
---
# <a name="deploy-azure-monitor-application-insights-agent-for-on-premises-servers"></a>オンプレミス サーバー用に Azure Monitor Application Insights エージェントをデプロイする

> [!IMPORTANT]
> このガイダンスは、Application Insights エージェントのオンプレミスと Azure 以外のクラウド デプロイに推奨されます。 [Azure 仮想マシンと仮想マシン スケール セットのデプロイ](https://docs.microsoft.com/azure/azure-monitor/app/azure-vm-vmss-apps)に推奨される方法を次に示します。

Application Insights エージェント (旧称 Status Monitor V2) は、[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)に公開されている PowerShell モジュールです。
これは [Status Monitor](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now) を置き換えるものです。
テレメトリが Azure portal に送信され、そこでアプリを[監視](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)できます。

> [!NOTE]
> このモジュールでは、現在、IIS でホストされる .NET Web アプリのコード不要のインストルメンテーションのみがサポートされます。 SDK を使用して、ASP.NET Core、Java、および Node.js アプリケーションをインストルメント化します。

## <a name="powershell-gallery"></a>PowerShell ギャラリー

Application Insights エージェントは、 https://www.powershellgallery.com/packages/Az.ApplicationMonitor にあります。

![PowerShell ギャラリー](https://img.shields.io/powershellgallery/v/Az.ApplicationMonitor.svg?color=Blue&label=Current%20Version&logo=PowerShell&style=for-the-badge)


## <a name="instructions"></a>Instructions
- 簡単なコード サンプルから始めるには、[概要の手順](status-monitor-v2-get-started.md)に関する記事を参照してください。
- 開始する方法の詳細については、[詳細な手順](status-monitor-v2-detailed-instructions.md)に関する記事を参照してください。

## <a name="powershell-api-reference"></a>PowerShell API リファレンス
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)
- [Start-ApplicationInsightsMonitoringTrace](status-monitor-v2-api-start-trace.md)

## <a name="troubleshooting"></a>トラブルシューティング
- [トラブルシューティング](status-monitor-v2-troubleshoot.md)
- [既知の問題](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>よく寄せられる質問

- Application Insights エージェントでプロキシのインストールはサポートされますか?

  *はい*。 Application Insights エージェントをダウンロードするには、複数の方法があります。 コンピューターがインターネットにアクセスできる場合は、`-Proxy` パラメーターを使用して PowerShell ギャラリーにオンボードできます。
このモジュールを手動でダウンロードし、コンピューターにインストールするか、直接使用することもできます。
これらの各オプションについては、[詳細な手順](status-monitor-v2-detailed-instructions.md)で説明しています。

- Status Monitor v2 では ASP.NET Core アプリケーションはサポートされていますか?

  *いいえ*。 ASP.NET Core アプリケーションの監視を有効にする手順については、「[Application Insights for ASP.NET Core アプリケーション](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)」を参照してください。 ASP.NET Core アプリケーション用に Status Monitor をインストールする必要はありません。 これは、ASP.NET Core アプリケーションが IIS でホストされている場合にも当てはまります。

- 有効化が成功したことを確認する方法を教えてください。

  - [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md) コマンドレットは、有効化が成功したことを確認するために使用できます。
  - アプリからテレメトリが送信されているかどうかをすばやく判断するには、[Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) を使用することをお勧めします。

  - [Log Analytics](../log-query/get-started-portal.md) を使用して、現在テレメトリを送信しているすべてのクラウド ロールを一覧表示することもできます。
      ```Kusto
      union * | summarize count() by cloud_RoleName, cloud_RoleInstance
      ```

## <a name="next-steps"></a>次のステップ

テレメトリの表示:

* パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
* 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
* より高度なクエリのために[分析を使用](../../azure-monitor/app/analytics.md)します。
* [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。

テレメトリの追加:

* サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
* Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
* トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。

