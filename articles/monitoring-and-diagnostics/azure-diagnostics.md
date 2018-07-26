---
title: Azure 診断拡張機能の概要
description: Azure 診断は、Cloud Services、Virtual Machines、および Service Fabric でのデバッグ、パフォーマンス測定、監視、トラフィック分析に使用できます。
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 07/13/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: b00d774ec59755288b8660d238c7b8dfc9a89eab
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2018
ms.locfileid: "39089895"
---
# <a name="what-is-azure-diagnostics-extension"></a>Azure 診断拡張機能とは何か
Azure 診断拡張機能は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内のエージェントです。 さまざまなソースで診断拡張機能を使用することができます。 現時点でのサポート対象は、Azure クラウド サービス (クラシック) の Web ロールおよび Worker ロール、仮想マシン、仮想マシン スケール セット、および Service Fabric となっています。 他の Azure サービスでは、異なる診断方法を使用します。 「[Azure Monitor の概要 ](monitoring-overview.md)」を参照してください。 

## <a name="linux-agent"></a>Linux エージェント
Linux を実行する仮想マシンには、[Linux 版の拡張機能](../virtual-machines/linux/diagnostic-extension.md)をご利用いただけます。 収集される統計情報と動作は、Windows 版とは異なります。 

## <a name="data-you-can-collect"></a>収集可能なデータ
Azure 診断拡張機能では、次の種類のデータを収集できます。

| [データ ソース] | 説明 |
| --- | --- |
| パフォーマンス カウンター |オペレーティング システムとカスタム パフォーマンス カウンター |
| Application Logs |アプリケーションによって書き込まれたメッセージをトレースする |
| Windows イベント ログ |Windows イベント ログ システムに送信された情報 |
| .NET イベント ソース |.NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) クラスを使用するコード作成イベント |
| IIS ログ |IIS Web サイトに関する情報 |
| マニフェスト ベースの ETW |すべてのプロセスによって生成された Windows イベント トレーシング イベント。(1) |
| クラッシュ ダンプ |アプリケーションがクラッシュした場合のプロセスの状態に関する情報 |
| カスタム エラー ログ |アプリケーションまたはサービスで作成されたログ |
| Azure 診断インフラストラクチャ ログ |診断自体に関する情報 |

(1) ETW プロバイダーの一覧を取得するには、情報を収集するコンピューターのコンソール ウィンドウで `c:\Windows\System32\logman.exe query providers` を実行します。 

## <a name="data-storage"></a>データ ストレージ
この拡張機能では、指定した [Azure Storage アカウント](azure-diagnostics-storage.md)にそのデータが格納されます。 

[Application Insights](../application-insights/app-insights-cloudservices.md) に送信することもできます。 [Event Hub](../event-hubs/event-hubs-what-is-event-hubs.md) にストリーム配信するという選択肢もあります。その場合、後で Azure 以外の監視サービスに送信できます。 


## <a name="versioning-and-configuration-schema"></a>バージョン管理と構成スキーマ
[Azure 診断のバージョン履歴とスキーマ](azure-diagnostics-versioning-history.md)に関するページを参照してください。


## <a name="next-steps"></a>次の手順
診断情報を収集するサービスを選択し、以下の記事の説明に従って操作を開始してください。 特定のタスクのリファレンスについては、一般的な Azure 診断リンクを使用してください。

## <a name="cloud-services-using-azure-diagnostics"></a>Cloud Services で Azure 診断を使用する
* Visual Studio を使用する場合は、[Visual Studio を使用した Cloud Services アプリケーションのトレース](../vs-azure-tools-debug-cloud-services-virtual-machines.md)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure 診断を使用して Cloud Services を監視する方法](../cloud-services/cloud-services-how-to-monitor.md)
* [Cloud Services アプリケーションでの Azure 診断の設定](../cloud-services/cloud-services-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。

* [Cloud Services 向けの Application Insights での Azure 診断の使用](../application-insights/app-insights-cloudservices.md)
* [Azure 診断で Cloud Services アプリケーションのフローをトレースする](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
* [PowerShell を使用した Cloud Services での診断の設定](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="virtual-machines"></a>Virtual Machines
* Visual Studio を使用する場合は、[Visual Studio を使用した Azure Virtual Machines のトレース](../vs-azure-tools-debug-cloud-services-virtual-machines.md)に関するページを参照して操作を開始してください。 それ以外の場合は次を参照してください。
* [Azure 仮想マシンでの Azure 診断の設定](../virtual-machines-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。

* [PowerShell を使用した Azure Virtual Machines での診断の設定](../virtual-machines/windows/ps-extensions-diagnostics.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Azure Resource Manager テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](../virtual-machines/windows/extensions-diagnostics-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="service-fabric"></a>Service Fabric
[Service Fabric アプリケーションの監視](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)に関するページを参照して操作を開始してください。 この記事にアクセスすると、左側のナビゲーション ツリーに Service Fabric 診断に関するその他の記事が多数あります。

## <a name="general-articles"></a>全般記事
* [Azure 診断でのパフォーマンス カウンターの使用](../cloud-services/diagnostics-performance-counters.md)について説明します。
* Azure Storage テーブルでの診断の開始またはデータの検索に問題がある場合は、[Azure 診断のトラブルシューティング](azure-diagnostics-troubleshooting.md)に関するページを参照してください。
