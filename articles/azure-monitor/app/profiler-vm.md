---
title: Application Insights Profiler を使用して Azure VM で実行されている Web アプリをプロファイルする | Microsoft Docs
description: Application Insights Profiler を使用して Azure VM 上の Web アプリをプロファイルします。
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: e8f80e7d19a961c22b4e1e88556ac165d2558034
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54081954"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Application Insights Profiler を使用して、Azure 仮想マシンまたは仮想マシン スケール セットで実行されている Web アプリをプロファイルする
Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>仮想マシンまたはスケール セットに Profiler をデプロイする
このページでは、Azure VM または Azure 仮想マシン スケール セットで Application Insights Profiler を実行するために必要な手順を説明します。 Application Insights Profiler は、VM 用の Windows の Azure 診断拡張機能と共にインストールされています。 拡張機能はプロファイラーを実行するように構成する必要があります。また、App Insights SDK をアプリケーションに組み込む必要があります。

1. Application Insights SDK を、お使いの [ASP.Net アプリケーション](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)または正規の [.NET アプリケーション](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json)に追加します。 要求のプロファイルを表示するには、要求テレメトリを Application Insights に送信する必要があります。
1. Windows の Microsoft Azure 診断拡張機能を VM にインストールします。 Resource Manager テンプレートの完全な例については、次を参照してください。  
    * [仮想マシン](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [仮想マシン スケール セット](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    重要な部分は、WadCfg の ApplicationInsightsProfilerSink です。 別のシンクをこのセクションに追加して、プロファイラーから iKey にデータを送信できるように WAD に指示します。
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. 変更された環境のデプロイ定義をデプロイします。  

   通常、変更を適用するには、完全なテンプレートのデプロイか、PowerShell コマンドレットまたは Visual Studio を使用したクラウド サービス ベースの発行が含まれます。  

   次の PowerShell コマンドは、Azure 診断拡張機能のみを使用する既存の仮想マシン向けの代替アプローチです。 必要な操作は、Get-AzureRmVMDiagnosticsExtension コマンドによって返される構成に、前述のように ProfilerSink を追加することのみです。 そして更新された構成を Set-AzureRmVMDiagnosticsExcension コマンドに渡します。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 経由で目的のアプリケーションが実行されている場合は、`IIS Http Tracing` Windows 機能を有効にします。

   a. この環境に対するリモート アクセスを確立し、[[Windows 機能の追加]]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ウィンドウ使用するか (管理者として) PowerShell で次のコマンドを実行します。  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. リモート アクセスの確立に問題がある場合は、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用して次のコマンドを実行できます。  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. アプリケーションをデプロイします。

## <a name="can-profiler-run-on-on-premises-servers"></a>オンプレミス サーバーで Profiler を実行できますか
オンプレミス サーバーで Application Insights Profiler をサポートすることは予定されていません。

## <a name="next-steps"></a>次の手順

- アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
- Azure ポータルで [Profiler トレース](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)を表示します。
- Profiler の問題をトラブルシューティングするための情報を [Profilerのトラブルシューティング](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json)に関するセクションで探します。
