---
title: Azure Application Insights エージェント API リファレンス
description: Application Insights エージェント API リファレンス。 Get-ApplicationInsightsMonitoringConfig。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 91e7bcbe79dfde8bbd4011748b40b692015af1c2
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899744"
---
# <a name="application-insights-agent-api-get-applicationinsightsmonitoringconfig"></a>Application Insights Agent API: Get-ApplicationInsightsMonitoringConfig

この記事では、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーであるコマンドレットについて説明します。

## <a name="description"></a>説明

構成ファイルを取得し、コンソールに値を出力します。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。

## <a name="examples"></a>例

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parameters

必須パラメーターはありません。

## <a name="output"></a>Output


#### <a name="example-output-from-reading-the-config-file"></a>構成ファイルの読み取りからの出力例

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
```

## <a name="next-steps"></a>次の手順

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
- より高度なクエリのために[分析](../../azure-monitor/app/analytics.md)を使用します。
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。
 
 Application Insights エージェントをさらに活用する:
 - Application Insights Agent の[トラブルシューティング](status-monitor-v2-troubleshoot.md)を行うには、このガイドを参照してください。
 - [Set config](status-monitor-v2-api-set-config.md) コマンドレットを使用して、構成に変更を加えます。
