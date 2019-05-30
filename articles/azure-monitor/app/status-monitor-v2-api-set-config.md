---
title: Azure Status Monitor v2 API リファレンス:構成を設定する | Microsoft Docs
description: Status Monitor v2 API リファレンス Set-ApplicationInsightsMonitoringConfig。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
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
ms.openlocfilehash: 971fea76a23859f32437a1698b6d3094113737a1
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255093"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>Status Monitor v2 API:Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

このドキュメントでは、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーとして付属しているコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="description"></a>説明

再インストールの全手順を繰り返さずに、構成ファイルを設定します。 変更を有効にするために IIS を再起動してください。

> [!IMPORTANT] 
> このコマンドレットでは、管理者権限の PowerShell セッションが必要です。


## <a name="examples"></a>例

### <a name="example-with-single-instrumentation-key"></a>単一のインストルメンテーション キーを使用した例
この例では、現在のマシン上のすべてのアプリケーションに単一のインストルメンテーション キーが割り当てられます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>インストルメンテーション キー マップを使用した例
この例では、 
- `MachineFilter` は `'.*'` ワイルドカードを使用して現在のマシンを照合します。
- `AppFilter='WebAppExclude'` は `null` InstrumentationKey を提供します。 このアプリはインストルメント化されません。
- `AppFilter='WebAppOne'` は、この特定のアプリに一意のインストルメンテーション キーを割り当てます。
- `AppFilter='WebAppTwo'` も、この特定のアプリに一意のインストルメンテーション キーを割り当てます。
- 最後に、`AppFilter` も `'.*'` ワイルドカードを使用して、以前の規則で一致していない他のすべての Web アプリと一致させ、既定のインストルメンテーション キーを割り当てます。
- スペースは、読みやすくするためだけに追加されます。

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parameters 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**必須。** このパラメーターを使用して、ターゲット マシン上のすべてのアプリケーションによって使用される単一の iKey を指定します。

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**必須。** このパラメーターを使用して、複数の ikeys と、どのアプリがどの ikey を使用するかを示すマッピングを指定します。 MachineFilter を設定することで、複数のマシン用に単一のインストール スクリプトを作成できます。 

> [!IMPORTANT] 
> アプリケーションは、指定された順番で規則と照合されます。 このため、最も具体的な規則を最初に指定し、最も一般的な規則を最後に指定する必要があります。

#### <a name="schema"></a>スキーマ
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** は、コンピューターまたは VM 名に必要な c# の正規表現です。
    - ".*" はすべてに一致します
    - "ComputerName" は正確に同じ名前のコンピューターのみと一致します。
- **AppFilter** は、コンピューターまたは VM 名に必要な c# の正規表現です。
    - ".*" はすべてに一致します
    - "ApplicationName" は正確に同じ名前の IIS アプリケーションのみと一致します。
- **InstrumentationKey** は上記の 2 つのフィルターに一致するアプリケーションの監視を有効にするために必要です。
    - 監視を除外する規則を定義する場合は、この値を null のままにします。


### <a name="-verbose"></a>-Verbose
**共通パラメーター**です。 詳細なログを出力する場合はこのスイッチを使用します。


## <a name="output"></a>Output

既定では出力はありません。

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>-InstrumentationKey を使用して構成ファイルを設定した場合の詳細出力の例

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>-InstrumentationKeyMap を使用して構成ファイルを設定した場合の詳細出力の例

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>次の手順

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します
- より高度なクエリのために [Analytics](../../azure-monitor/app/analytics.md) を使用します
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを挿入するために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します
 
 Status Monitor v2 の活用:
 - ガイドを使用して、Status Monitor v2 を[トラブルシューティング](status-monitor-v2-troubleshoot.md)します。
 - 設定が正しく記録されたことを確認するために[構成を取得](status-monitor-v2-api-get-config.md)します。
 - 監視を検査するために[状態を取得](status-monitor-v2-api-get-status.md)します。
