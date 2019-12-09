---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/15/2019
ms.openlocfilehash: a01258799efa81c8d3ddba398facaa90c24c2513
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150031"
---
# <a name="overview-of-the-azure-monitor-agents"></a>Azure Monitor エージェントの概要 
仮想マシンなどのコンピューティングリ ソースでは、[他のクラウド リソース](../insights/monitor-azure-resource.md)と同様に、自身のパフォーマンスと可用性を監視するためのデータが生成されます。 また、コンピューティング リソースには、監視を必要とする、ゲスト オペレーティング システムとワークロードも含まれています。 この監視データをリソース内から収集するには、エージェントが必要です。 この記事では、Azure Monitor で使用されるエージェントについて説明します。特定の環境の要件を満たす上で必要なエージェントを判断するのに役に立ちます。

## <a name="summary-of-agents"></a>エージェントの概要

> [!NOTE]
> Azure Monitor と Log Analytics が最近統合されたため、現在 Azure Monitor には複数のエージェントがあります。 両方のエージェントで共有されている機能もあれば、特定のエージェントに固有の機能もあります。 要件によって、1 つのエージェントだけ必要な場合もあれば、両方のエージェントが必要になる場合もあります。 

Azure Monitor には 3 つのエージェントがあり、それぞれに固有の機能が用意されています。 要件に応じて、ご利用の仮想マシンとその他のコンピューティング リソースに 1 つまたは複数のエージェントをインストールすることができます。

* [Azure Diagnostics 拡張機能](#azure-diagnostic-extension)
* [Log Analytics エージェント](#log-analytics-agent)
* [依存関係エージェント](#dependency-agent)

次の表は、さまざまなエージェントを簡単に比較したものです。 それぞれの詳細については、この記事の残りの部分を参照してください。

| | Azure 診断の拡張機能 | Log Analytics エージェント | 依存関係エージェント |
|:---|:---|:---|:---|
| サポートされている環境 | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス |
| オペレーティング システム | Windows<br>Linux | Windows<br>Linux | Windows<br>Linux
| エージェントの依存関係  | なし | なし | Log Analytics エージェントが必要 |
| 収集されるデータ | イベント ログ<br>ETW イベント<br>syslog<br>パフォーマンス<br>IIS ログ<br>.NET アプリのトレース出力のログ<br>クラッシュ ダンプ | イベント ログ<br>syslog<br>パフォーマンス<br>IIS ログ<br>カスタム ログ<br>ソリューションからのデータ | プロセスの詳細と依存関係<br>ネットワーク接続のメトリック |
| 送信されるデータ | Azure Storage<br>Azure Monitor メトリック<br>イベント ハブ | Azure Monitor ログ | Azure Monitor ログ |



## <a name="azure-diagnostic-extension"></a>Azure 診断の拡張機能
[Azure Diagnostics 拡張機能](../../azure-monitor/platform/diagnostics-extension-overview.md)を使うと、Azure コンピューティング リソースのゲスト オペレーティング システムとワークロードから監視データを収集できます。 それによってデータは主に Azure Storage に収集されます。 ストレージから Log Analytics ワークスペースにデータをコピーするように Azure Monitor を構成することができます。 また、Azure Monitor メトリックにゲスト パフォーマンス データを収集することもできます。

Azure 診断の拡張機能は、多くの場合、Windows Azure 診断 (WAD) の拡張機能または Linux Azure Diagnostics (LAD) の拡張機能と呼ばれます。


### <a name="scenarios-supported"></a>サポートされるシナリオ

Azure Diagnostics の拡張機能でサポートされるシナリオは次のとおりです。

* Azure コンピューティング リソースからログとパフォーマンス データを収集します。
* ゲスト オペレーティング システムから Azure ストレージにログとパフォーマンス データをアーカイブします。
* [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールを使用して、ストレージ内の監視データを表示します。
* メトリック データベースにパフォーマンス データを収集して、[Azure Monitor メトリック](data-platform-metrics.md)でサポートされている機能 (ほぼリアルタイムの[メトリック アラート](../../azure-monitor/platform/alerts-metric-overview.md)や[自動スケーリング](autoscale-overview.md)など) を活用します。 
* [ストレージから Log Analytics ワークスペース](azure-storage-iis-table.md)に監視データを収集して、[ログ クエリ](../log-query/log-query-overview.md)など、[Azure Monitor ログ](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)でサポートされている機能を活用します。
* [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md) を使用して、監視データをサードパーティ製のツールに送信します。
* [ブート診断](../../virtual-machines/troubleshooting/boot-diagnostics.md)を使用して VM ブートの問題を調査する。
* ご利用の VM で実行されているアプリケーションから [Application Insights に](diagnostics-extension-to-application-insights.md)データをコピーして、他のアプリケーション監視と統合します。

## <a name="log-analytics-agent"></a>Log Analytics エージェント
[Log Analytics エージェント](log-analytics-agent.md)を使うと、Azure、他のクラウド プロバイダー、オンプレミスの仮想マシンのゲスト オペレーティング システムとワークロードから、監視データを収集できます。 データは Log Analytics ワークスペースに収集されます。

Log Analytics エージェントは、System Center Operations Manager によって使用されるものと同じであり、エージェント コンピューターをマルチホーム化して管理グループおよび Azure Monitor との通信を同時に行います。 このエージェントは、Azure Monitor の特定のソリューションにも必要です。

Windows 用 Log Analytics エージェントは、多くの場合、Microsoft Management Agent (MMA) と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、OMS エージェントと呼ばれます。


### <a name="scenarios-supported"></a>サポートされるシナリオ

Log Analytics エージェントによってサポートされるシナリオは次のとおりです。

* Azure、その他のクラウド プロバイダー、およびオンプレミスにある仮想マシンからログとパフォーマンスデータを収集します。 
* 監視データを Log Analytics ワークスペースに収集して、[ログ クエリ](../log-query/log-query-overview.md)など、[Azure Monitor ログ](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs)でサポートされている機能を活用します。
* [Azure Monitor for VMs](../insights/vminsights-overview.md)、[コンテナー用 Azure Monitor](../insights/container-insights-overview.md) などの Azure Monitor 監視ソリューションを使用します。  
* Log Analytics エージェントを必要とする [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用の仮想マシンのセキュリティを管理します。
* Log Analytics エージェントを必要とする [Azure Security Center](../../security-center/security-center-intro.md) を使用して、ご利用の仮想マシンのセキュリティを管理します。
* [Azure Automation](../../automation/automation-intro.md) の機能を使用して、ご利用の Azure VM の包括的な管理を、そのライフサイクルを通して行います。  Log Analytics エージェントを必要とするこれらの機能の例を次に示します。
  * [Azure Automation Update](../../automation/automation-update-management.md) による、オペレーティング システム更新プログラムの管理。
  * [Azure Automation State Configuration](../../automation/automation-dsc-overview.md) による、構成状態の一貫性の維持。
  * [Azure Automation Change Tracking および Inventory](../../automation/change-tracking.md) による、構成変更の追跡。

## <a name="dependency-agent"></a>依存関係エージェント
依存関係エージェントでは、仮想マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データが収集されます。 このエージェントは、[Service Map](../insights/service-map.md) とマップ機能 [Azure Monitor for VMs](../insights/vminsights-overview.md) とに必要です。 依存関係エージェントでは、Log Analytics エージェントが必要とされ、Azure Monitor の Log Analytics ワークスペースにデータが書き込まれます。


## <a name="using-multiple-agents"></a>複数のエージェントの使用
特定の仮想マシンに対して Azure 診断の拡張機能または Log Analytics エージェントのいずれかを使用するための特定の要件がある場合があります。 たとえば、Azure 診断の拡張機能を必要とするメトリック アラートを使用する必要があります。 しかし、依存関係エージェントと Log Analytics エージェントを必要とする Azure Monitor for VMs のマップ機能も使用する必要があるとします。 この場合、複数のエージェントを使用できます。これは、それぞれのエージェントの機能を必要とする顧客を対象にした一般的なシナリオです。

### <a name="considerations"></a>考慮事項

- 依存関係エージェントを使用するには、Log Analytics エージェントが同じ仮想マシンにインストールされている必要があります。
- Linux VM の場合は、Azure 診断の拡張機能の前に Log Analytics エージェントをインストールする必要があります。


## <a name="next-steps"></a>次の手順

- [Log Analytics エージェントの概要](../../azure-monitor/platform/log-analytics-agent.md)に関する記事を参照して、Azure、データ センター、または他のクラウド環境でホストされているマシンにエージェントをデプロイするための要件とサポートされている方法を確認します。

