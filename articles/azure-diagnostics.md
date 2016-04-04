<properties
	pageTitle="Azure 診断の概要"
	description="Azure 診断は、Cloud Services、Virtual Machines、および Service Fabric でのデバッグ、パフォーマンス測定、監視、トラフィック分析に使用できます。"
	services="multiple"
	documentationCenter=".net"
	authors="rboucher"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/20/2016"
	ms.author="robb"/>


# Microsoft Azure 診断とは


Azure 診断は、デプロイされたアプリケーションで診断データを収集できるようにする Azure 内の機能です。さまざまなソースで診断拡張機能を使用することができます。現在時点でのサポート対象は、Azure Cloud Service Web および Worker ロール、Microsoft Windows を実行している Azure Virtual Machines、および Service Fabric となっています。その他の Azure サービスには、独自の独立した診断があります。

## 収集可能なデータ

Azure 診断は、次の種類のデータを収集できます。

データ ソース|説明
---|---
パフォーマンス カウンター | オペレーティング システムとカスタム パフォーマンス カウンター
Application Logs | アプリケーションによって書き込まれたメッセージをトレースする
Windows イベント ログ | Windows イベント ログ システムに送信された情報
.NET イベント ソース | .NET [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) クラスを使用するコード作成イベント
IIS ログ | IIS Web サイトに関する情報
マニフェスト ベースの ETW | すべてのプロセスによって生成された Windows イベントのイベント トレース
クラッシュ ダンプ | アプリケーションがクラッシュした場合のプロセスの状態に関する情報
カスタム エラー ログ | アプリケーションまたはサービスで作成されたログ
Azure 診断インフラストラクチャ ログ|診断自体に関する情報

Azure 診断拡張機能は、このデータを Azure ストレージ アカウントに転送したり、[Application Insights](./application-insights/app-insights-cloudservices.md) のようなサービスに送信したりすることができます。データは、デバッグ、トラブルシューティング、パフォーマンス測定、リソース使用状況の監視、トラフィック解析と容量計画、監査などに使用できます。


## バージョン管理
「[Azure Diagnostics Versioning History (Azure 診断のバージョン履歴)](azure-diagnostics-versioning-history.md)」を参照してください。

## 次のステップ
診断情報を収集するサービスを選択し、以下の記事の説明に従って操作を開始してください。特定のタスクのリファレンスについては、一般的な Azure 診断リンクを使用してください。

## Web Apps
Web Apps では Azure 診断が使用されないので注意してください。Azure 診断に相当する情報については、[Web Apps](./app-service-web/web-sites-enable-diagnostic-log.md) を参照してください。

## Cloud Services で Azure 診断を使用する
- Visual Studio を使用する場合は、「[Use Visual Studio to trace a Cloud Services application (Visual Studio を使用して Cloud Services アプリケーションをトレースする)](./vs-azure-tools-debug-cloud-services-virtual-machines.md)」を参照してください。それ以外の場合は次を参照してください。
- [How to monitor Cloud services using Azure Diagnostics (Azure 診断を使用して Cloud Services を監視する方法)](./cloud-services/cloud-services-how-to-monitor.md)
- [Set up Azure Diagnostics in a Cloud Services Application(Cloud Services アプリケーションで Azure 診断を設定する)](./cloud-services/cloud-services-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。
- [Using Azure Diagnostics with Application Insights for Cloud Services (Cloud Services 向けの Application Insights での Azure 診断の使用)](./application-insights/app-insights-cloudservices.md)
- [Azure 診断で Cloud Services アプリケーションのフローをトレースする](./cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)
- [Use PowerShell to set up diagnostics on Cloud Services (PowerShell を使用して Cloud Services で診断を設定する)](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)


## Virtual Machines で Azure 診断を使用する
- Visual Studio を使用する場合は、[Use Visual Studio to trace Azure Virtual Machines (Visual Studio を使用して Azure Virtual Machines をトレースする)](./vs-azure-tools-debug-cloud-services-virtual-machines.md) を参照して操作を開始してください。それ以外の場合は次を参照してください。
- [Set up Azure Diagnostics on an Azure Virtual Machine (Azure 仮想マシンで Azure 診断を設定する)](./virtual-machines-dotnet-diagnostics.md)

詳細なトピックについては、次を参照してください。
- [Use PowerShell to set up diagnostics on Azure Virtual Machines (PowerShell を使用して Azure Virtual Machines で診断を設定する)](./virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md)
- [Azure リソース マネージャー テンプレートを使用して監視および診断を含む Windows 仮想マシンを登録する](./virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md)

## Service Fabric で Azure 診断を使用する
[Monitor a Service Fabric application (Service Fabric アプリケーションを監視する)](./service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) を参照して使用を開始します。この記事にアクセスすると、左側のナビゲーション ツリーに Service Fabric 診断に関するその他の記事が多数あります。

## Azure 診断に関する一般的な記事
- [Azure Diagnostics Schema Configuration (Azure 診断スキーマの構成)](https://msdn.microsoft.com/library/azure/mt634524.aspx) - 診断データを収集して送信するようにスキーマ ファイルを変更する方法について説明します。Visual Studio を使用してスキーマ ファイルを変更することもできます。
- [How Azure Diagnostics data is stored in Azure Storage (Azure Storage に Azure 診断データを格納する方法)](./cloud-services/cloud-services-dotnet-diagnostics-storage.md) - 診断データが書き込まれるテーブルと BLOB の名前を示します。
- [Azure アプリケーションでのパフォーマンス カウンターの使用](./cloud-services/cloud-services-dotnet-diagnostics-performance-counters.md)について説明します。　
- [Application Insights への Azure 診断情報の送信](./azure-diagnostics-configure-applicationinsights.md)について説明します。　
- Azure Storage テーブルでの診断の開始またはデータの検索に問題がある場合は、「[TroubleShooting Azure Diagnostics (Azure 診断のトラブルシューティング)](./azure-diagnostics-troubleshooting.md)」を参照してください。

<!---HONumber=AcomDC_0323_2016-->