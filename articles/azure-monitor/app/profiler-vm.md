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
ms.openlocfilehash: 4dfe4140fbc4238af014d838d31aae167cdd1c16
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54438764"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Application Insights Profiler を使用して、Azure 仮想マシンまたは仮想マシン スケール セットで実行されている Web アプリをプロファイルする

Azure Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>仮想マシンまたは仮想マシン スケール セットに Profiler をデプロイする
この記事では、Azure 仮想マシン (VM) または Azure 仮想マシン スケール セットで Application Insights Profiler を実行する方法を説明します。 Profiler は、VM 用の Azure 診断拡張機能と共にインストールされます。 Profiler を実行するように拡張機能を構成し、Application Insights SDK をお使いのアプリケーションにビルドします。

1. Application Insights SDK を、お使いの [ASP.NET アプリケーション](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net)または正規の [.NET アプリケーション](windows-services.md?toc=/azure/azure-monitor/toc.json)に追加します。  
  要求のプロファイルを表示するには、要求テレメトリを Application Insights に送信する必要があります。

1. Azure 診断拡張機能を VM にインストールします。 Resource Manager テンプレートの完全な例については、次を参照してください。  
    * [仮想マシン](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [仮想マシン スケール セット](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    重要な部分は、WadCfg の ApplicationInsightsProfilerSink です。 Microsoft Azure 診断で Profiler がお使いの iKey にデータを送信できるようにするには、このセクションに他のシンクを追加します。
    
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

   通常、変更の適用には、完全なテンプレートのデプロイか、PowerShell コマンドレットまたは Visual Studio を使用したクラウド サービス ベースの発行が含まれます。  

   次の PowerShell コマンドは、Azure 診断拡張機能のみを使用する既存の仮想マシン向けの代替アプローチです。 前述の ProfilerSink を、Get-AzureRmVMDiagnosticsExtension コマンドによって返された構成に追加し、更新された構成を Set-AzureRmVMDiagnosticsExtension コマンドに渡します。

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. [IIS](https://www.microsoft.com/web/downloads/platform.aspx) 経由で目的のアプリケーションが実行されている場合は、`IIS Http Tracing` Windows 機能を有効にします。

   a. この環境に対するリモート アクセスを確立し、[[Windows 機能の追加]]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ウィンドウ使用するか、 (管理者として) PowerShell で次のコマンドを実行します。  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. リモート アクセスの確立に問題がある場合は、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用して次のコマンドを実行できます。  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. アプリケーションをデプロイします。

## <a name="can-profiler-run-on-on-premises-servers"></a>オンプレミスのサーバーで Profiler を実行できますか。
オンプレミス サーバーで Application Insights Profiler をサポートすることは予定されていません。

## <a name="next-steps"></a>次の手順

- アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
- Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
- Profiler の問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関する記事をご覧ください。
