---
title: Application Insights を使用してライブ Azure Service Fabric アプリケーションをプロファイルする | Microsoft Docs
description: Service Fabric アプリケーションでプロファイラーを有効にする
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
ms.openlocfilehash: 2513511326ff5574e8dcf3eedfde977cf9877efd
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082346"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights を使用してライブ Azure Service Fabric アプリケーションをプロファイルする

Application Insights Profiler を次のサービスにデプロイすることもできます。
* [Azure App Service](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>環境デプロイ定義を設定する

Application Insights Profiler は、Windows Azure 診断 (WAD) に付属しています。 WAD 拡張機能は、Service Fabric クラスターの Azure RM テンプレートを使用してインストールできます。 次に示すのはテンプレートの例です。[**Service Fabric クラスターに WAD をインストールするテンプレート。**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

環境を設定するには、次の操作を実行します。
1. [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 以降が使用されていることを確認するために、デプロイされている OS が `Windows Server 2012 R2` 以降であることを確認します。

1. デプロイ テンプレート ファイルで [Azure 診断](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics)拡張機能を探し、`WadCfg` の子要素として次の `SinksConfig` セクションを追加します。 `ApplicationInsightsProfiler` プロパティ値は、自分の Application Insights のインストルメンテーション キーに置き換えます。  

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
1. Azure Resource Manager テンプレートを使用して、Service Fabric クラスターをデプロイします。 設定が正しい場合は、Application Insights Profiler がインストールされ、WAD 拡張機能のインストール時に有効になります。 
1. Application Insights を Service Fabric アプリケーションに追加します。 プロファイラーで要求のプロファイルを収集するためには、アプリケーションが Application Insights に要求データを送信する必要があります。 手順は[こちら](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)を参照してください。
1. アプリケーションを再デプロイします。

> [ヒント] Virtual Machines では、上記の json ベースの手順に代わる方法として、Azure portal にで **[Virtual Machines]** > **[診断設定]** > **[シンク]** に移動し、Application Insights への診断データの送信を **[有効]** に設定し、Application Insights アカウントまたは特定の ikey を選択します。

## <a name="next-steps"></a>次の手順

- アプリケーションへのトラフィックを生成します (たとえば、[可用性テスト](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)を起動します)。 その後、Application Insights インスタンスへのトレースの送信が開始されるまで 10 ～ 15 分待機します。
- Azure ポータルで [Profiler トレース](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json)を表示します。
- Profiler の問題をトラブルシューティングするための情報を [Profilerのトラブルシューティング](profiler-troubleshooting.md ?toc=/azure/azure-monitor/toc.json)に関するセクションで探します。