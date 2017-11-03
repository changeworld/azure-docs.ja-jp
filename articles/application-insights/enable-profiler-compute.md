---
title: "Azure コンピューティング アプリケーションの Application Insights Profiler を有効にする | Microsoft Docs"
description: "Azure コンピューティングを実行しているアプリケーションで Profiler をセットアップする方法について説明します。"
services: application-insights
documentationcenter: 
author: ramach
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: b39b3b234112647ebeb531262d3b3876aee0b63b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2017
---
# <a name="enable-application-insights-profiler-on-azure-virtual-machines-service-fabric-and-cloud-services"></a>Azure Virtual Machines、Service Fabric、および Cloud Services で Application Insights Profiler を有効にする

このチュートリアルでは、Azure コンピューティング リソースによってホストされる ASP.NET アプリケーションで Azure Application Insights Profiler を有効にする方法を示します。  
例には、Azure Virtual Machines、Azure Virtual Machine Scale Sets、Azure Service Fabric、および Azure Cloud Services のサポートが含まれています。  
例は、[Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview) デプロイ モデルをサポートするテンプレートを活用しています。  


## <a name="overview"></a>概要

次の図に、Profiler が Azure リソースを利用する方法を示します。 例として、Azure 仮想マシンを使用しています。

![概要](./media/enable-profiler-compute/overview.png)


Profiler を完全に有効にするには、3 つの場所で構成を完了する必要があります。

1. Application Insights インスタンス ポータル ブレード。
2. アプリケーション ソース コード (例: ASP.NET Web アプリケーション)。
3. 環境デプロイ定義ソース コード (例: Virtual Machine デプロイ テンプレート json ファイル)。


## <a name="configure-the-application-insights-instance"></a>Application Insights インスタンスを構成する

使用する Application Insights インスタンス用の Azure Portal ブレードを作成するかアクセスし、インストルメンテーション キーをメモします。 このキーは、他の構成手順で使用します。

  ![キーの場所](./media/enable-profiler-compute/CopyAIKey.png)

このインスタンスは、要求ごとにテレメトリ データを送信するようにアプリケーションが更新されたインスタンスと同じです。
また、このインスタンスでは Profiler の結果を使用できるようになります。  

Azure Portal を開いたままで、[Profiler を有効にする](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)方法に従って Profiler 用に AI インスタンスの構成を完了します。 このチュートリアルの目的では Web アプリをリンクする必要はありません。Azure Portal で Profiler が有効になっていることだけを確認してください。


## <a name="configure-the-application-source-code"></a>アプリケーションのソース コードを構成する

各 `Request` 操作で、テレメトリ データを Application Insights インスタンスに送信するようにアプリケーションを構成する必要があります。  

1. [Application Insights SDK](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-overview#get-started) をアプリケーション プロジェクトに追加します。 NuGet パッケージが次のバージョンであることを確認します。  
  - ASP.NET アプリケーションの場合: バージョン 2.3.0 以降の [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)。
  - ASP.NET Core アプリケーションの場合: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 以降。
  - 他の .NET および .NET Core アプリケーションの場合 (例: Service Fabric ステートレス サービス、Cloud Services worker ロール): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) または [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 以降。  

2. アプリケーションが ASP.NET または ASP.NET Core アプリケーション*ではない*場合 (例: Cloud Services worker ロール、Service Fabric Stateless API)、追加で次のインストルメンテーションの設定が必要です。  

  2.1. アプリケーションの有効期間の初期段階に次のコードを追加します。  

  ```csharp
  using Microsoft.ApplicationInsights.Extensibility;
  ...
  // Replace with your own Application Insights instrumentation key.
  TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
  ```
  このグローバル インストルメンテーション キーの構成については、「[Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)」(Service Fabric と Application Insights を使用する) を参照してください。  

  2.2. インストルメント化するすべてのコードについて、次の例のように、その近くに `StartOperation<RequestTelemetry>` `using` ステートメントを追加します。

  ```csharp
  using Microsoft.ApplicationInsights;
  using Microsoft.ApplicationInsights.DataContracts;
  ...
  var client = new TelemetryClient();
  ...
  using (var operation = client.StartOperation<RequestTelemetry>("Insert_Your_Custom_Event_Unique_Name"))
  {
    // ... Code I want to profile.
  }
  ```

> [!NOTE]  
> 別の `StartOperation<RequestTelemetry>` スコープ内で `StartOperation<RequestTelemetry>` を呼び出すことはサポートされません。 代わりに、入れ子にしたスコープで `StartOperation<DependencyTelemetry>` を使用できます。 例:  

```csharp
using (var getDetailsOperation = client.StartOperation<RequestTelemetry>("GetProductDetails"))
{
  try
  {
    ProductDetail details = new ProductDetail() { Id = productId };
    getDetailsOperation.Telemetry.Properties["ProductId"] = productId.ToString();

    // By using DependencyTelemetry, 'GetProductPrice' is correctly linked as part of the 'GetProductDetails' request.
    using (var getPriceOperation = client.StartOperation<DependencyTelemetry>("GetProductPrice"))
    {
        double price = await _priceDataBase.GetAsync(productId);
        if (IsTooCheap(price))
        {
            throw new PriceTooLowException(productId);
        }
        details.Price = price;
    }

    // Similarly, note how 'GetProductReviews' doesn't establish another RequestTelemetry.
    using (var getReviewsOperation = client.StartOperation<DependencyTelemetry>("GetProductReviews"))
    {
        details.Reviews = await _reviewDataBase.GetAsync(productId);
    }

    getDetailsOperation.Telemetry.Success = true;
    return details;
  }
  catch(Exception ex)
  {
    getDetailsOperation.Telemetry.Success = false;

    // This exception gets linked to the 'GetProductDetails' request telemetry.
    client.TrackException(ex);
    throw;
  }
}
```


## <a name="configure-the-environment-deployment-definition"></a>環境のデプロイ定義を構成する

Profiler とアプリケーションが実行される環境は、Virtual Machine、Virtual Machine Scale Set、Service Fabric Cluster、または Cloud Services の可能性があります。  

### <a name="virtual-machine-virtual-machine-scale-sets-or-service-fabric"></a>Virtual Machine、Virtual Machine Scale Sets、または Service Fabric

詳細な例:  
  * [仮想マシン](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [Virtual Machine Scale Set](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric Cluster](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するには、デプロイされている OS が `Windows Server 2012 R2` 以降であることを確認します。

2. デプロイ テンプレート ファイルで [Azure 診断](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)拡張機能を探し、`WadCfg` の子要素として次の `SinksConfig` セクションを追加します。このとき、`ApplicationInsightsProfiler` プロパティ値を独自の AI インストルメンテーション キーで置き換えます。  
```json
"SinksConfig": {
  "Sink": [
    {
      "name": "MyApplicationInsightsProfilerSink",
      "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
    }
  ]
}
```

デプロイ テンプレートに診断拡張機能を追加する方法については、例と[こちらのガイド](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を参照してください。


### <a name="cloud-services"></a>Cloud Services

1. [.NET Framework 4.6.1](https://docs.microsoft.com/en-us/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するには、`ServiceConfiguration.*.cscfg` ファイルに含まれている `osFamily` が `"5"` 以降であることを確認するだけで十分です。

2. アプリケーション ロールで [Azure 診断](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/azure-diagnostics)の `diagnostics.wadcfgx` ファイルを探します。  
![診断構成ファイルの場所](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
ファイルが見つからない場合、Azure Cloud Services プロジェクトで診断拡張機能を有効にする方法については、[こちらのガイド](https://docs.microsoft.com/en-us/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)を参照してください。

3. `WadCfg` の子要素として次の `SinksConfig` セクションを追加します。  
  ```xml
  <WadCfg>
    <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
    <SinksConfig>
      <Sink name="MyApplicationInsightsProfiler">
        <!-- Replace with your own Application Insights instrumentation key. -->
        <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
      </Sink>
    </SinksConfig>
  </WadCfg>
  ```

> [!NOTE]  
> `diagnostics.wadcfgx` ファイルに種類が `ApplicationInsights` の別のシンクが含まれている場合、これら 3 つのインストルメンテーション キーが一致する必要があります。  
>  * アプリケーションで使用されるインストルメンテーション キー  
>  * `ApplicationInsights` シンクで使用されるインストルメンテーション キー  
>  * `ApplicationInsightsProfiler` シンクで使用されるインストルメンテーション キー  
>
>シンクによって実際に使用されているインストルメンテーション キー値は、`ApplicationInsights`ファイルで確認することができます`ServiceConfiguration.*.cscfg`。  
>Visual Studio 15.5 Azure SDK リリース以降、アプリケーションと `ApplicationInsightsProfiler` シンクに使用されるインストルメンテーション キーは相互に一致する必要があるようになりました。


## <a name="environment-deployment-and-runtime-configurations"></a>環境のデプロイとランタイム構成

1. 変更された環境のデプロイ定義をデプロイします。  
通常、変更を適用するには、完全なテンプレートをデプロイするか、PowerShell コマンドレットまたは Visual Studio を使用した Cloud Services を発行します。  
診断拡張機能のみを使用する既存の `Virtual Machines` 向けの代替アプローチを次に紹介します。  
```powershell
$ConfigFilePath = [IO.Path]::GetTempFileName()
# After exporting the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
(Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
# Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
# if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
# (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
```

2. [IIS](https://www.microsoft.com/web/platform/server.aspx) 経由で目的のアプリケーションが実行されている場合、`IIS Http Tracing` Windows 機能を有効にする必要があります。  
  この環境に対するリモート アクセスを確立し、[[Windows 機能の追加]]( https://docs.microsoft.com/en-us/iis/configuration/system.webserver/tracing/) ウィンドウ使用するか (管理者として) PowerShell で以下を実行します。  
  ```powershell
  Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
  ```  
  リモート アクセスの確立に問題がある場合は、[Azure CLI](https://docs.microsoft.com/en-us/cli/azure/get-started-with-azure-cli) を使用して以下を実行できます。  
  ```powershell
  az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
  ```


## <a name="how-to-enable-profiler-on-on-premises-servers"></a>オンプレミス サーバーで Profiler を有効にする方法

スタンドアロン モードでの AI Profiler の実行とも呼ばれます (診断拡張機能の変更に関連していません)。  
オンプレミス サーバー向けに Profiler を公式にサポートする予定はありません。
このシナリオを実験したい場合は、[こちら](https://github.com/ramach-msft/AIProfiler-Standalone)からサポート コードをダウンロードできます。  
Microsoft では、このコードを保守したり、このコードに関連する問題と機能リクエストに対応したりする責任を*負いません*。


## <a name="next-steps"></a>次のステップ

- アプリケーションに対するトラフィックを生成し (例: [可用性テスト](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-monitor-web-app-availability)の実行)、それから 10 ～ 15 分後に Application Insights インスタンスに送信されるトレースを開始します。

- Azure Portal の [Profiler のトレース](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)に関するセクションを参照してください。

- Profiler の問題をトラブルシューティングするためのヘルプを [Profilerのトラブルシューティング](app-insights-profiler.md#troubleshooting)に関するセクションで探します。

- [Application Insights Profiler](app-insights-profiler.md) で Profiler の詳細を確認します。
