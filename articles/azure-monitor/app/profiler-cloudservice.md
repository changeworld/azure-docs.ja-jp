---
title: Application Insights でライブ Azure Cloud Services をプロファイリングする | Microsoft Docs
description: Azure Cloud Services に対して Application Insights Profiler を有効にします。
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
ms.openlocfilehash: 76512a2c930f44ae5a9b57d85ca34544788a538a
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435891"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights でライブ Azure Cloud Services をプロファイリングする

Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric アプリケーション](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler は、Azure 診断拡張機能と共にインストールされています。 必要な作業は、Profiler をインストールして Application Insights リソースにプロファイルを送信するように、Azure 診断を構成することだけです。

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services に対して Profiler を有効にする
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降を使用していることを確認します。 *ServiceConfiguration.\*.cscfg* ファイルの `osFamily` 値が "5" 以上になっていることを確認すれば十分です。

1. [Application Insights SDK を Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json) に追加します。

1. Application Insights で要求を追跡します。

    * ASP.NET Web ロールの場合、Application Insights で要求を自動的に追跡できます。

    * worker ロールの場合、[要求を追跡するコードを追加します](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)。

1. 次のようにして、Profiler を有効にするように Azure 診断拡張機能を構成します。

    a. アプリケーション ロールで、[Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)の *diagnostics.wadcfgx* ファイルを探します。  

      ![診断構成ファイルの場所](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      ファイルが見つからない場合は、「[Set up diagnostics for Azure Cloud Services and Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them)」(Azure クラウド サービスと仮想マシンに対する診断を設定する) をご覧ください。

    b. `WadCfg` の子要素として次の `SinksConfig` セクションを追加します。  

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
    > *diagnostics.wadcfgx* ファイルに ApplicationInsights 型の別のシンクが含まれている場合、次の 3 つのインストルメンテーション キーがすべて一致する必要があります。  
    > * アプリケーションによって使用されるキー。 
    > * ApplicationInsights シンクによって使用されるキー。 
    > * ApplicationInsightsProfiler シンクによって使用されるキー。 
    >
    > `ApplicationInsights` シンクで実際に使用されているインストルメンテーション キーの値は、*ServiceConfiguration.\*.cscfg* ファイルで確認できます。 
    > Visual Studio 15.5 Azure SDK リリース以降は、アプリケーションと ApplicationInsightsProfiler シンクで使用されるインストルメンテーション キーのみが相互に一致する必要があります。

1. 新しい診断構成でサービスをデプロイすると、ご利用のサービスで Application Insights Profiler が実行されるように構成されます。
 
## <a name="next-steps"></a>次の手順

* アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](monitor-web-app-availability.md)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
* Azure ポータルで [Profiler トレース](profiler-overview.md?toc=/azure/azure-monitor/toc.json)を表示します。
* プロファイラーの問題のトラブルシューティングについては、[Profiler のトラブルシューティング](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)に関するページをご覧ください。
