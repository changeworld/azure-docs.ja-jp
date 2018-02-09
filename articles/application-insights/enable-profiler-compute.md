---
title: "Azure コンピューティング アプリケーションの Application Insights Profiler を有効にする | Microsoft Docs"
description: "Azure コンピューティングを実行しているアプリケーションで Application Insights Profiler を設定する方法について説明します。"
services: application-insights
documentationcenter: 
author: ramach-msft
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: ramach
ms.openlocfilehash: d4559007aece8850b4c2d707686effd706ec468c
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2018
---
# <a name="enable-application-insights-profiler-for-azure-vms-service-fabric-and-cloud-services"></a>Azure VM、Service Fabric、および Cloud Services で Application Insights Profiler を有効にする

この記事では、Azure コンピューティング リソースによってホストされる ASP.NET アプリケーションで Azure Application Insights Profiler を有効にする方法を示します。

この記事の例には、Azure Virtual Machines、仮想マシン スケール セット、Azure Service Fabric、および Azure Cloud Services のサポートが含まれています。 例は、[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) デプロイ モデルをサポートするテンプレートを活用しています。  


## <a name="overview"></a>概要

次の図は、Application Insights Profiler が Azure コンピューティング リソースとどのように連携するかを示しています。 Azure コンピューティング リソースには、Virtual Machines、Virtual Machine Scale Sets、Cloud Services、Service Fabric クラスターなどがあります。 図では、例として、Azure 仮想マシンを使用しています。  

  ![概要](./media/enable-profiler-compute/overview.png)

Profiler を完全に有効にするには、3 つの場所で構成を変更する必要があります。

* Azure Portal の Application Insights インスタンス ブレード。
* アプリケーションソース コード (例: ASP.NET Web アプリケーション)。
* 環境デプロイ定義ソース コード (例: .json ファイル形式の Azure Resource Manager テンプレート)。


## <a name="set-up-the-application-insights-instance"></a>Application Insights インスタンスを設定する

[新しい Application Insights リソースを作成](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-create-new-resource)するか、または既存の Application Insights リソースを選択します。
Application Insights リソースに移動して、インストルメンテーション キーをコピーしてください。

  ![インストルメンテーション キーの場所](./media/enable-profiler-compute/CopyAIKey.png)

次に、「[プロファイラーを有効にする](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler)」の手順を完了して、Profiler 用の Application Insights インスタンスの設定を完了します。 Web アプリをリンクさせる必要はありません。その手順は、App Services リソースでのみ必要となります。 プロファイラーの *[構成]* ブレードで、プロファイラーが有効になっていることだけを確認してください。


## <a name="set-up-the-application-source-code"></a>アプリケーションのソース コードを設定する

### <a name="aspnet-web-applications-cloud-services-web-roles-or-service-fabric-aspnet-web-frontend"></a>ASP.NET Web アプリケーション、Cloud Services Web ロール、Service Fabric ASP.NET Web フロントエンド
`Request` 操作ごとに、テレメトリ データを Application Insights インスタンスに送信するようにアプリケーションを設定します。  

[Application Insights SDK](https://docs.microsoft.com/azure/application-insights/app-insights-overview#get-started) をアプリケーション プロジェクトに追加します。 NuGet パッケージが次のバージョンであることを確認します。  
  - ASP.NET アプリケーションの場合: [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 以降。
  - ASP.NET Core アプリケーションの場合: [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) 2.1.0 以降。
  - 他の .NET および .NET Core アプリケーションの場合 (例: Service Fabric ステートレス サービスや Cloud Services worker ロール): [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) または [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 2.3.0 以降。  

### <a name="cloud-services-worker-roles-or-service-fabric-stateless-backend"></a>Cloud Services worker ロールまたは Service Fabric Stateless バックエンド
アプリケーションが ASP.NET または ASP.NET Core アプリケーション "*ではない*" 場合 (例: Cloud Services worker ロールや Service Fabric Stateless API の場合)、前出の手順に加え、次のインストルメンテーションの設定が必要です。  

  1. アプリケーションの有効期間の初期に次のコードを追加します。  

    ```csharp
    using Microsoft.ApplicationInsights.Extensibility;
    ...
    // Replace with your own Application Insights instrumentation key.
    TelemetryConfiguration.Active.InstrumentationKey = "00000000-0000-0000-0000-000000000000";
    ```
  このグローバル インストルメンテーション キーの構成については、「[Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)」(Service Fabric と Application Insights を使用する) を参照してください。  

  2. インストルメント化するすべてのコードについて、次の例のように、その近くに `StartOperation<RequestTelemetry>` **USING** ステートメントを追加します。

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

  別の `StartOperation<RequestTelemetry>` スコープ内で `StartOperation<RequestTelemetry>` を呼び出すことはサポートされません。 代わりに、入れ子にしたスコープで `StartOperation<DependencyTelemetry>` を使用できます。 例:   

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


## <a name="set-up-the-environment-deployment-definition"></a>環境デプロイ定義を設定する

Profiler とアプリケーションが実行される環境は、仮想マシン、仮想マシン スケール セット、Service Fabric クラスター、または Cloud Services インスタンスが可能です。  

### <a name="virtual-machines-virtual-machine-scale-sets-or-service-fabric"></a>仮想マシン、仮想マシン スケール セット、または Service Fabric

詳細な例:  
  * [仮想マシン](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
  * [仮想マシン スケール セット](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
  * [Service Fabric クラスター](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するために、デプロイされている OS が `Windows Server 2012 R2` 以降であることを確認します。

2. デプロイ テンプレート ファイルで [Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)拡張機能を探し、`WadCfg` の子要素として次の `SinksConfig` セクションを追加します。 `ApplicationInsightsProfiler` プロパティ値は、自分の Application Insights のインストルメンテーション キーに置き換えます。  
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

  デプロイ テンプレートへの診断拡張機能の追加の詳細については、「[Windows VM と Azure Resource Manager テンプレートで監視と診断を利用する](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。


### <a name="cloud-services"></a>Cloud Services

1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するには、ServiceConfiguration.\*.cscfg ファイルの `osFamily` の値が **"5"** 以降であることを確認すれば十分です。

2. アプリケーション ロールで、[Azure 診断の ](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) diagnostics.wadcfgx ファイルを探します。  
  ![診断構成ファイルの場所](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  
  ファイルが見つからない場合は、「[Azure クラウド サービスと仮想マシンに対する診断を設定する](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)」で、Cloud Services プロジェクトで診断拡張機能を有効にする方法を確認してください。

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
> `diagnostics.wadcfgx` ファイルに種類が `ApplicationInsights` の別のシンクが含まれている場合、次の 3 つのインストルメンテーション キーがすべて一致する必要があります。  
>  * アプリケーションで使用されるインストルメンテーション キー。  
>  * `ApplicationInsights` シンクで使用されるインストルメンテーション キー。  
>  * `ApplicationInsightsProfiler` シンクで使用されるインストルメンテーション キー。  
>
> `ApplicationInsights` シンクで実際に使用されているキーの値は、ServiceConfiguration.\*.cscfg ファイルで確認できます。  
> Visual Studio 15.5 Azure SDK リリース以降は、アプリケーションと `ApplicationInsightsProfiler` シンクで使用されるインストルメンテーション キーのみが相互に一致する必要があります。


## <a name="environment-deployment-and-runtime-configurations"></a>環境のデプロイとランタイム構成

1. 変更された環境のデプロイ定義をデプロイします。  

  通常、変更を適用するには、完全なテンプレートをデプロイするか、PowerShell コマンドレットまたは Visual Studio を使用してクラウド サービスを発行します。  

  Azure 診断拡張機能のみを使用する既存の仮想マシン向けの代替アプローチを次に紹介します。  
  ```powershell
  $ConfigFilePath = [IO.Path]::GetTempFileName()
  # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
  (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
  # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
  # if your original diagnostics configuration had the storageAccountName property in the protectedSettings section
  # (which is not downloadable). Make sure to pass the same original value you had in this cmdlet call.
  Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
  ```

2. [IIS](https://www.microsoft.com/web/platform/server.aspx) 経由で目的のアプリケーションが実行されている場合は、`IIS Http Tracing` Windows 機能を有効にします。  

  1. この環境に対するリモート アクセスを確立し、[[Windows 機能の追加]]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ウィンドウ使用するか (管理者として) PowerShell で次のコマンドを実行します。  
    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
  2. リモート アクセスの確立に問題がある場合は、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) を使用して次のコマンドを実行できます。  
    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```


## <a name="enable-the-profiler-on-on-premises-servers"></a>オンプレミス サーバーで Profiler を有効にする

オンプレミス サーバーでの Profiler の有効化は、別名 Application Insights Profiler のスタンドアロン モードでの実行と言います (Azure 診断拡張機能の変更と関連付けられていません)。

オンプレミス サーバー向けに Profiler を公式にサポートする予定はありません。 このシナリオを実験したい場合は、[サポート コードをダウンロード](https://github.com/ramach-msft/AIProfiler-Standalone)できます。 Microsoft では、このコードを保守したり、このコードに関連する問題と機能リクエストに対応したりする責任を*負いません*。

## <a name="next-steps"></a>次の手順

- アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
- Azure ポータルで [Profiler トレース](https://docs.microsoft.com/azure/application-insights/app-insights-profiler#enable-the-profiler)を表示します。
- Profiler の問題をトラブルシューティングするための情報を [Profilerのトラブルシューティング](app-insights-profiler.md#troubleshooting)に関するセクションで探します。
- [Application Insights Profiler](app-insights-profiler.md) で profiler の詳細を確認します。
