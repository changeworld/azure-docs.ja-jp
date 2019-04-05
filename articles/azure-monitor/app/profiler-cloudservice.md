---
title: Application Insights でライブ Azure Cloud Services をプロファイリングする | Microsoft Docs
description: Azure Cloud Services に対して Application Insights Profiler を有効にします。
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 2e13f1f09fcdfb68a99e705511e3659f1632132e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57895483"
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

   >**Cloud Services 向けの WAD の最新バージョンに付属しているプロファイラーにはバグがあります。** クラウド サービスでプロファイラーを使用するために、サポートされる AI SDK はバージョン 2.7.2 までのみです。 AI SDK の新しいバージョンを使用している場合は、プロファイラーを使用するために、2.7.2 に戻す必要があります。 Visual Studio を使用して、App Insights SDK のバージョンをダウングレードすると、実行時にバインド リダイレクト エラーが発生する可能性があります。 これは、AI SDK のダウングレード後に web.config ファイル内の Microsoft.ApplicationInsights の "newVersion" を "2.7.2.0" に設定する必要がありますが、自動的に更新されないからです。

1. Application Insights で要求を追跡します。

    * ASP.NET Web ロールの場合、Application Insights で要求を自動的に追跡できます。

    * worker ロールの場合、[要求を追跡するコードを追加します](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)。

1. 次のようにして、Profiler を有効にするように Azure 診断拡張機能を構成します。

    a. アプリケーション ロールで、[Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)の *diagnostics.wadcfgx* ファイルを探します。  

      ![診断構成ファイルの場所](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      ファイルが見つからない場合は、「[Set up diagnostics for Azure Cloud Services and Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)」(Azure クラウド サービスと仮想マシンに対する診断を設定する) をご覧ください。

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
