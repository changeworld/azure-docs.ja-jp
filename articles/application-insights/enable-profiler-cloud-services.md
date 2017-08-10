---
title: "Compute リソース 用に Azure Application Insights Profiler を有効化 | Microsoft Docs"
description: "Profiler の設定方法の詳細"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: cfreeman
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: cde0b0bce2e332243e555a72088b8e9eeb680bdb
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="how-to-enable-application-insights-profiler-on-azure-compute-resources"></a>Azure Compute リソースで Application Insights Profiler を有効にする方法

このチュートリアルでは、Azure Compute リソースによってホストされる ASP.NET アプリケーションで Application Insights Profiler を有効にする方法を示します。 この例には、Virtual Machines、仮想マシン スケール セット、および Services Fabric のサポートが含まれています。 この例は、すべて Azure Resource Management デプロイメント モデルをサポートするテンプレートを活用しています。 デプロイメント モデルについて詳しくは、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](/azure-resource-manager/resource-manager-deployment-model)」を参照してください。

## <a name="overview"></a>概要

次の図に、Profiler が Azure Compute リソースを利用する方法を示します。 例として、Azure Virtual Machine を使用しています。

![概要](./media/enable-profiler-compute/overview.png)Azure Portal で処理および表示するための情報を収集するには Azure Compute リソースの診断エージェント コンポーネントをインストールする必要があります。 このチュートリアルの残りの部分では、診断エージェントをインストールして構成し、Application Insights Profiler を有効にする方法を説明します。

## <a name="prerequisites-for-the-walkthrough"></a>このチュートリアルの前提条件

* VM またはスケール セット に Profiler エージェントをインストールするデプロイメント Resource Manager テンプレートをダウンロードします。

    [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json) | [WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json)
* プロファイリングのために有効化されている Application Insights インスタンス。 https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler を参照して、実行する方法を確認してください。
* .NET framework >= 4.6.1 がターゲットの Azure Compute リソースにインストールされています。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Azure サブスクリプションにリソース グループを作成する
PowerShell スクリプトを使用してリソース グループを作成する方法を次の例で説明します。

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>リソース グループでの Application Insights リソースの作成

![Application Insights の作成](./media/enable-profiler-compute/createai.png)

## <a name="apply-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートでの Application Insights のインストルメンテーション キーの適用
テンプレートをまだダウンロードしていない場合は、下記の場所からダウンロードしてください。 [WindowsVirtualMachine.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachine.json)

![AI キーの検索](./media/enable-profiler-compute/copyaikey.png)

![テンプレートの値の置換](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-azure-vm-to-host-the-web-application"></a>Web アプリをホストする Azure VM の作成
* パスワードを保護するセキュアな文字列の作成
```
$password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
```

* Azure Resource Manager テンプレートのデプロイ

PowerShell コンソール内のディレクトリを Resource Manager テンプレートが含まれているフォルダーに変更します。 テンプレートをデプロイするには、以下のコマンドを実行します。

```
New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
```

スクリプトが正常に実行されると、リソース グループ内に *MyWindowsVM* という名前の VM ができます。

## <a name="configure-web-deploy-on-the-vm"></a>VM での Web デプロイの構成
**Web デプロイ**が VM で有効になっており、Web アプリケーションを Visual Studio から公開できることを確認してください。

Web デプロイは VM 上に WebPI 経由で手動でインストールできます。[IIS 8.0 以降への Web デプロイのインストールおよび構成](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)

Azure Resource Manager テンプレートを使用して Web デプロイのインストールを自動化する方法の例を示します。[Azure VM への Web アプリの作成、構成、およびデプロイ](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)

ASP.NET MVC アプリケーションを展開する場合、Server Manager の **[役割と機能の追加] | [Web サーバー (IIS)] | [Web サーバー] | [Application Development] (アプリケーション開発)** へ移動して、サーバーで ASP.NET 4.5 を有効にする必要があります。
![Add ASP.NET](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-azure-application-insights-sdk-to-your-project"></a>Azure Application Insights SDK のプロジェクトへのインストール
* Visual Studio で ASP.NET Web アプリを開く
* プロジェクトを右クリックして、**[追加] | [接続されたサービス]** を選択する
* [Application Insights] (アプリケーション インサイト) を選択する
* このページで説明されている手順に従う。 先ほど作成した Application Insights リソースを選択する
* **[登録]** ボタンをクリックする


## <a name="publish-the-project-to-azure-vm"></a>プロジェクトを Azure VM に公開する
Azure VM にアプリケーションを公開するにはいくつかの方法があります。 その 1 つは、Visual Studio 2017 を使用する方法です。
公開プロセスを完了するには、プロジェクトを右クリックして [Publish...].(発行...) を選択します。 公開ターゲットとして Azure Virtual Machine を選択し、次の手順に従います。

![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

アプリケーションに対してロード テストを実行します。 結果は Application Insights のインスタンス ポータル の Web ページで確認できます。


## <a name="enable-the-profiler-in-application-insights"></a>Application Insights での Profiler の有効化
Application Insights の [パフォーマンス] ブレードに移動します。 [設定] アイコンと [プロファイラーを有効にする] をクリックします。

![Profiler の有効化手順 1](./media/enable-profiler-compute/enableprofiler1.png)

![Profiler の有効化手順 2](./media/enable-profiler-compute/enableprofiler2.png)

## <a name="add-an-availability-test-to-your-application"></a>可用性テストのアプリケーションへの追加
先ほど作成した Application Insights リソースを参照します。 [可用性] ブレードに移動し、アプリケーションの URL に Web リクエストを送信するパフォーマンス テストを追加します。 このようにして、Application Insights Profiler に表示されるサンプル データを収集することができます。

![Add Performance Test][./media/enable-profiler-compute/add-test.png]

## <a name="view-your-performance-data"></a>パフォーマンス データの表示

Profiler がデータを収集して分析するまで 10 - 15 分待ちます。 AI リソースの [パフォーマンス] ブレードに移動し、アプリケーションに負荷がかかったときのパフォーマンスを表示します。

![View Performance][./media/enable-profiler-compute/view-aiperformance.png]

[Examples with open the Trace View] ブレードの下のアイコンをクリックします。

![Trace View][./media/enable-profiler-compute/traceview.png]


## <a name="work-with-an-existing-template"></a>既存のテンプレートの使用

1. デプロイ テンプレートで Windows Azure 診断 (WAD) リソース宣言を見つけます。
  * もしまだない場合は作成します (作成の方法は詳細な例で確認してください)。
  * Azure Resource の Web サイト (https://resources.azure.com) でテンプレートを更新することができます。
2. 公開元を "Microsoft.Azure.Diagnostics" から "AIP.Diagnostics.Test" に変更します。
3. typeHandlerVersion を "0.0" として使用します。
4. autoUpgradeMinorVersion が true に設定されていることを確認します。
5. 次の例に示すように、新しい ApplicationInsightsProfiler シンク インスタンスを WadCfg settings オブジェクトに追加します。

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets での Profiler の有効化
[WindowsVirtualMachineScaleSet.json](https://github.com/CFreemanwa/samples/blob/master/WindowsVirtualMachineScaleSet.json) テンプレートをダウンロードして、Profiler を有効にする方法を確認します。 VM テンプレートと同じ変更を仮想マシン スケール セットの分析拡張リソースに適用します。
スケール セット内の各インスタンスがインターネットにアクセスできることを確認します。これによって、Profiler Agent が収集したサンプルを Application Insights に送信し、分析および表示することができます。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Service Fabric アプリケーションでの Profiler の有効化
現在、Service Fabric アプリケーションで Profiler を有効にするには、次のことが必要です。
1. Service Fabric クラスター が Profiler エージェントをインストールする WAD 拡張を持つようプロビジョニングする
2. Application Insights SDK をプロジェクトにインストールし、 AI キーを構成する
3. アプリケーション コードにインストルメント テレメトリを追加する

## <a name="provision-the-service-fabric-cluster-have-the-wad-extension-that-installs-the-profiler-agent"></a>Service Fabric クラスター が Profiler エージェントをインストールする WAD 拡張を持つようプロビジョニングする
Service Fabric クラスターはセキュリティ保護するかしないかを選択できます。 1 つのゲートウェイ クラスターをセキュリティ保護しない設定とすることで、アクセスに証明書が不要となります。 ビジネス ロジックとデータをホストするクラスターは、セキュリティで保護する必要があります。 Profiler は、セキュリティ保護されている Service Fabric クラスターとセキュリティ保護されていない Service Fabric クラスターの両方で有効にすることができます。 このチュートリアルでは、Profiler を有効にするために必要な変更を説明する目的で、セキュリティ保護されていないクラスターを例として使用しています。 セキュリティ保護されたクラスターも同じ方法でプロビジョニングできます。

[ServiceFabricCluster.json](https://github.com/CFreemanwa/samples/blob/master/ServiceFabricCluster.json) をダウンロードします。 VM および仮想マシン スケール セットと同様に、Application Insights キーを AI キーで置き換えます。

```
"publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
```

PowerShell スクリプトを使用してテンプレートをデプロイします。
```
Login-AzureRmAccount
New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

```

## <a name="install-application-insights-sdk-in-the-project-and-configure-ai-key"></a>Application Insights SDK をプロジェクトにインストールし、 AI キーを構成する
Install Application Insights SDK を NuGet Package からインストールします。 安定バージョンの 2.3 以降をインストールします。 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) プロジェクトでの Application Insights の構成については、[Application Insights での Service Fabric の使用](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md) を参照してください。

## <a name="add-application-code-to-instrument-telemetry"></a>アプリケーション コードにインストルメント テレメトリを追加する
インストルメント化するすべてのコードについて、周辺に "using" ステートメントを追加します。 次の例では、下の RunAsync メソッドによって、telemetryClient クラスが開始からテレメトリをキャプチャします。 イベントにはアプリケーション全体で一意の名前を付ける必要があります。

```
protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
```

Service Fabric クラスターにアプリケーションをデプロイします。 アプリの実行まで 10 分待ちます。 さらに効果を高めるため、アプリのロード テストも実行できます。 Application Insights ポータルの [パフォーマンス] ブレードで、プロファイルのトレースの例が表示されるのが確認できます。

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>次のステップ

- プロファイラーの問題のトラブルシューティングに関するヘルプを探す [Profiler troubleshooting](app-insights-profiler.md#troubleshooting)

- プロファイラーについて詳しく読む [Application Insights Profiler](app-insights-profiler.md).

