---
title: Azure Status Monitor v2 API リファレンス:監視を有効にする | Microsoft Docs
description: Status Monitor v2 API リファレンス Enable-ApplicationInsightsMonitoring。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
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
ms.openlocfilehash: 17798c78b167821f5f30a11996ac90cf67fb0179
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/06/2019
ms.locfileid: "65144959"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>Status Monitor v2 API:Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

このドキュメントでは、[Az.ApplicationMonitor PowerShell モジュール](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/)のメンバーとして付属しているコマンドレットについて説明します。

> [!IMPORTANT]
> 現在、Status Monitor v2 はパブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください

## <a name="description"></a>説明

ターゲット マシンでコードを使用しない接続の監視を有効にします。
このコマンドレットは、IIS applicationHost.config を変更し、いくつかのレジストリ キーを設定します。
また、このコマンドレットは、どのインストルメンテーション キーがどのアプリケーションによって使用されるかを定義する applicationinsights.ikey.config を作成します。
IIS は起動時に RedfieldModule を読み込みます。これにより、アプリケーションの起動時に、Application Insights SDK がこれらのアプリケーションに挿入されます。
変更を有効にするために IIS を再起動してください。

監視を有効にしたら、[ライブ メトリック](live-stream.md)を使用して、アプリケーションがテレメトリを送信しているかどうかをすばやく確認します。


> [!NOTE] 
> 開始するには、インストルメンテーション キーが必要です。 詳細については、[新しいリソースの作成](create-new-resource.md#copy-the-instrumentation-key)に関する記事を参照してください。


> [!IMPORTANT] 
> このコマンドレットでは、管理者権限および昇格された実行ポリシーを持つ PowerShell セッションが必要です。 詳細については、[こちら](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy)をご覧ください。

> [!NOTE] 
> このコマンドレットでは、ライセンスおよびプライバシーに関する声明を確認して同意する必要があります。


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


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**省略可能。** インストルメンテーション エンジンを有効にして、管理されたプロセスの実行中に何が発生したかを示すイベントとメッセージを収集する場合は、このスイッチを使用します。 依存関係の結果コード、HTTP 動詞、および SQL コマンド テキストが含まれますが、これに限定されません。 インストルメンテーション エンジンはオーバーヘッドを追加します。このエンジンは、既定ではオフになっています。

### <a name="-acceptlicense"></a>-AcceptLicense
**省略可能。** ヘッドレス インストールでライセンスおよびプライバシーに関する声明に同意する場合は、このスイッチを使用します。

### <a name="-verbose"></a>-Verbose
**共通パラメーター**です。 詳細なログを出力する場合はこのスイッチを使用します。

### <a name="-whatif"></a>-WhatIf 
**共通パラメーター**です。 実際に監視を有効にせずに、入力パラメーターをテストして検証する場合は、このスイッチを使用します。

## <a name="output"></a>Output


#### <a name="example-output-from-a-successful-enablement"></a>正常に有効化された場合の出力例

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

## <a name="next-steps"></a>次の手順

  テレメトリの表示:
 - パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します
- より高度なクエリのために [Analytics](../../azure-monitor/app/analytics.md) を使用します
- [ダッシュボードを作成](../../azure-monitor/app/app-insights-dashboards.md)します
 
 テレメトリの追加:
 - サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを挿入するために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。
 
 Status Monitor v2 をさらに活用します。
 - ガイドを使用して、Status Monitor v2 を[トラブルシューティング](status-monitor-v2-troubleshoot.md)します。
 - 設定が正しく記録されたことを確認するために[構成を取得](status-monitor-v2-api-get-config.md)します。
 - 監視を検査するために[状態を取得](status-monitor-v2-api-get-status.md)します。
