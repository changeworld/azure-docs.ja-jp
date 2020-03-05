---
title: Azure Application Insights Agent - 概要 | Microsoft Docs
description: Application Insights Agent のクイックスタート ガイド。 Web サイトを再デプロイせずに Web サイトのパフォーマンスを監視します。 オンプレミス、VM、または Azure でホストされた ASP.NET Web アプリが対象です。
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 7819de1f3dfab7f934421de86c0481d2e063f7a4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2020
ms.locfileid: "77671190"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>オンプレミス サーバー向け Azure Monitor Application Insights Agent の概要

この記事には、ほとんどの環境で動作するクイックスタート コマンドが含まれています。
これらの手順では、更新の配布において PowerShell ギャラリーに依存します。
これらのコマンドでは、PowerShell `-Proxy` パラメーターがサポートされます。

これらのコマンドの説明、カスタマイズの手順、トラブルシューティングの情報については、[詳細な手順](status-monitor-v2-detailed-instructions.md)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="download-and-install-via-powershell-gallery"></a>PowerShell ギャラリーからダウンロードしてインストールする

### <a name="install-prerequisites"></a>必須コンポーネントのインストール
PowerShell を管理者として実行します。
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
PowerShell を閉じます。

### <a name="install-application-insights-agent"></a>Application Insights Agent をインストールする
PowerShell を管理者として実行します。
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>監視を有効にする
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>手動でダウンロードしてインストールする (オフライン オプション)
### <a name="download-the-module"></a>モジュールをダウンロードする
[PowerShell ギャラリー](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)からモジュールの最新バージョンを手動でダウンロードします。

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights Agent のファイルを解凍してインストールする
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>監視を有効にする
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>次のステップ

 テレメトリの表示:

- パフォーマンスと使用状況を監視するための[メトリックを探索](../../azure-monitor/app/metrics-explorer.md)します。
- 問題を診断するために[イベントとログを検索](../../azure-monitor/app/diagnostic-search.md)します。
- より高度なクエリのために[分析を使用](../../azure-monitor/app/analytics.md)します。
- [ダッシュボードを作成](../../azure-monitor/app/overview-dashboard.md)します。

 テレメトリの追加:

- サイトがライブの状態であることを確認するために [Web テストを作成](monitor-web-app-availability.md)します。
- Web ページ コードからの例外を参照してトレースの呼び出しを有効にするために、[Web クライアント テレメトリ](../../azure-monitor/app/javascript.md)を追加します。
- トレースとログの呼び出しを挿入できるように、[Application Insights SDK をコードに追加](../../azure-monitor/app/asp-net.md)します。

Application Insights エージェントをさらに活用する:

- ここに記載されているコマンドの説明については、[詳細な手順](status-monitor-v2-detailed-instructions.md)に関する記事を参照してください。
- Application Insights エージェントのトラブルシューティングを行う場合は、[こちらのガイド](status-monitor-v2-troubleshoot.md)を使用してください。
