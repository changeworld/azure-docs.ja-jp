---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/02/2020
ms.openlocfilehash: 48d233dee063d8f9049e6e54baa2d744e1b8b058
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033457"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor エージェントの概要

仮想マシンおよびその他のコンピューティング リソースでは、ゲスト オペレーティング システムとワークロードのパフォーマンスと可用性を測定するために必要な監視データを収集するエージェントが必要です。 この記事では、Azure Monitor で使用されるエージェントについて説明します。特定の環境の要件を満たす上で必要なエージェントを判断するのに役に立ちます。

> [!NOTE]
> Azure Monitor と Log Analytics が最近統合されたため、現在 Azure Monitor には複数のエージェントがあります。 機能に重複がある場合もありますが、それぞれに固有の機能があります。 要件によっては、仮想マシンに 1 つ以上のエージェントが必要になる場合があります。 

特定の仮想マシンに対して、1 つのエージェントでは完全に満たすことができない特定の要件セットがある場合があります。 たとえば、Azure Diagnostics 拡張機能を必要とするメトリック アラートを使用する必要があるときに、Log Analytics エージェントと依存関係エージェントを必要とする Azure Monitor for VM の機能も利用したい場合があります。 このような場合、複数のエージェントを使用できます。これは、それぞれの機能を必要とする顧客を対象にした一般的なシナリオです。

## <a name="summary-of-agents"></a>エージェントの概要

次の表は、Windows と Linux の Azure Monitor エージェントを簡単に比較したものです。 それぞれの詳細については、以下のセクションで説明します。

> [!NOTE]
> Azure Monitor エージェントは、現在プレビューの段階にあり、機能が制限されています。 この表は更新される予定です 

### <a name="windows-agents"></a>Windows エージェント

| | Azure Monitor エージェント (プレビュー) | 診断<br>拡張機能 (WAD) | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | 
| **エージェントの要件**  | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | イベント ログ<br>パフォーマンス | イベント ログ<br>ETW イベント<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>.NET アプリ ログ<br>クラッシュ ダンプ<br>エージェント診断ログ | イベント ログ<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>分析情報とソリューション<br>その他のサービス | プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック | Azure Storage<br>Azure Monitor メトリック<br>イベント ハブ | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | メトリックス エクスプローラー | VM に対する Azure Monitor<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM に対する Azure Monitor<br>サービス マップ |

### <a name="linux-agents"></a>Linux エージェント

| | Azure Monitor エージェント (プレビュー) | 診断<br>拡張機能 (LAD) | Telegraf<br>エージェント | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス |
| **エージェントの要件**  | なし | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | syslog<br>パフォーマンス | syslog<br>パフォーマンス | パフォーマンス | syslog<br>パフォーマンス| プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック | Azure Storage<br>イベント ハブ | Azure Monitor メトリック | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | | メトリックス エクスプローラー | VM に対する Azure Monitor<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM に対する Azure Monitor<br>サービス マップ |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor エージェント (プレビュー)
[Azure Monitor エージェント](azure-monitor-agent-overview.md)は、現在プレビューの段階にあり、Windows と Linux の両方の仮想マシンで、Log Analytics エージェントおよび Telegraf エージェントを置き換えます。 Azure Monitor ログと Azure Monitor のメトリックの両方にデータを送信し、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用します。このルールは、各エージェントのデータ コレクションと変換先をよりスケーラブルに構成する方法を提供します。

次のことを行う必要がある場合は、Azure Monitor エージェントを使用します。

- Azure、その他のクラウド、またはオンプレミスの任意の仮想マシンからゲスト ログとメトリックを収集する。 (Azure のみプレビュー段階にあります。)
- Azure Monitor での分析のために、Azure Monitor ログと Azure Monitor メトリックにデータを送信する。 
- アーカイブのためにデータを Azure Storage に送信する。
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [Azure Security Center](../../security-center/security-center-intro.md) または [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用の仮想マシンのセキュリティを管理する。 (プレビューでは使用できません。)

Azure Monitor エージェントの制限事項は次のとおりです。

- 現在、パブリック プレビュー段階にあります。 パブリック プレビュー段階での制限事項の一覧については、[現在の制限事項](azure-monitor-agent-overview.md#current-limitations)に関する記事を参照してください。

## <a name="log-analytics-agent"></a>Log Analytics エージェント

[Log Analytics エージェント](log-analytics-agent.md)を使うと、Azure、他のクラウド プロバイダー、オンプレミスの仮想マシンのゲスト オペレーティング システムとワークロードから、監視データを収集できます。 データは、Log Analytics ワークスペースに送信されます。 Log Analytics エージェントは、System Center Operations Manager によって使用されるものと同じであり、エージェント コンピューターをマルチホーム化して管理グループおよび Azure Monitor との通信を同時に行います。 このエージェントは、Azure Monitor および Azure の他のサービスの特定の分析情報にも必要です。


> [!NOTE]
> Windows 用 Log Analytics エージェントは、多くの場合、Microsoft Monitoring Agent (MMA) と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、OMS エージェントと呼ばれます。



Log Analytics エージェントは、次のような場合に使用します。

* Azure の内部または外部の仮想または物理マシンからログとパフォーマンス データを収集する。 
* データを Log Analytics ワークスペースに送信して、[ログ クエリ](../log-query/log-query-overview.md)など、[Azure Monitor ログ](data-platform-logs.md)でサポートされている機能を活用する。
* 仮想マシンを大規模に監視し、そのプロセスや他のリソースおよび外部プロセスに対する依存関係を監視できる [Azure Monitor for VMs](../insights/vminsights-overview.md) を使用する。  
* [Azure Security Center](../../security-center/security-center-intro.md) または [Azure Sentinel](../../sentinel/overview.md) を使用して、ご利用の仮想マシンのセキュリティを管理する。
* [Azure Automation Update Management](../../automation/update-management/update-mgmt-overview.md)、[Azure Automation State Configuration](../../automation/automation-dsc-overview.md)、または [Azure Automation Change Tracking および Inventory](../../automation/change-tracking.md) を使用して、Azure VM の包括的な管理を提供する
* さまざまな[ソリューション](../monitor-reference.md#insights-and-core-solutions)を使用して、特定のサービスまたはアプリケーションを監視する。

Log Analytics エージェントの制限事項は次のとおりです。

- Azure Monitor メトリック、Azure Storage、または Azure Event Hubs にデータを送信することはできません。
- 個々のエージェントに対して一意の監視定義を構成することは困難です。
- 各仮想マシンに固有の構成があるため、大規模な管理は困難です。

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能

[Azure Diagnostics 拡張機能](diagnostics-extension-overview.md)によって、Azure 仮想マシンおよびその他のコンピューティング リソースのゲスト オペレーティング システムとワークロードから監視データが収集されます。 データは主に Azure Storage に収集されますが、データ シンクを定義して、Azure Monitor メトリックや Azure Event Hubs などの他の送信先にデータを送信することもできます。

Azure Diagnostics 拡張機能は、次のような場合に使用します。

- アーカイブのため、または [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールで分析したりするために Azure Storage にデータを送信する。
- [Azure Monitor メトリック](data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](./alerts-metric-overview.md)や[自動スケーリング](autoscale-overview.md) (Windows のみ) などの機能を活用したりする。
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [ブート診断](../../virtual-machines/troubleshooting/boot-diagnostics.md)を収集して VM ブートの問題を調査する。

Azure Diagnostics 拡張機能の制限事項は次のとおりです。

- Azure リソースでのみ使用できます。
- Azure Monitor ログにデータを送信する機能が制限されています。

## <a name="telegraf-agent"></a>Telegraf エージェント

[InfluxData Telegraf エージェント](collect-custom-metrics-linux-telegraf.md)は、Linux コンピューターから Azure Monitor メトリックにパフォーマンス データを収集するために使用されます。

Telegraf エージェントは、次のような場合に使用します。

* [Azure Monitor メトリック](data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](./alerts-metric-overview.md)や[自動スケーリング](autoscale-overview.md) (Linux のみ) などの機能を活用したりする。 



## <a name="dependency-agent"></a>依存関係エージェント

依存関係エージェントでは、仮想マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データが収集されます。 

依存関係エージェントは、次のような場合に使用します。

* マップ機能の [Azure Monitor for VMs](../insights/vminsights-overview.md) または [Service Map](../insights/service-map.md) ソリューションを使用する。

依存関係エージェントを使用する場合は、次の点を考慮してください。

- 依存関係エージェントを使用するには、Log Analytics エージェントが同じ仮想マシンにインストールされている必要があります。
- Linux VM の場合は、Azure 診断の拡張機能の前に Log Analytics エージェントをインストールする必要があります。

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

[Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics 拡張機能は、Azure 仮想マシンに Log Analytics エージェントをインストールします。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の Azure Monitor 依存関係拡張機能は、Azure 仮想マシンに依存関係エージェントをインストールします。 これらは上記と同じエージェントですが、これらは[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)を使用して管理できます。 可能な場合は、拡張機能を使用してエージェントをインストールおよび管理する必要があります。


## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
次の表は、Azure Monitor エージェントでサポートされているオペレーティング システムの一覧です。 エージェント固有の考慮事項とインストール プロセスについては、各エージェントのドキュメントをご覧ください。 Telegraf でサポートされているオペレーティング システムについては、Telegraf のドキュメントをご覧ください。 オペレーティング システムはすべて x64 と見なされます。 x86 はどのオペレーティング システムでもサポートされていません。

### <a name="windows"></a>Windows

| オペレーティング システム | Azure Monitor エージェント | Log Analytics エージェント | 依存関係エージェント | 診断拡張機能 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(マルチセッションを含む) および Pro  | X | X | X | X |
| Windows 8 Enterprise および Pro                             |   | X | X |   |
| Windows 7 SP1                                            |   | X | X |   |


### <a name="linux"></a>Linux

| オペレーティング システム | Azure Monitor エージェント | Log Analytics エージェント | 依存関係エージェント | 診断拡張機能 | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7.8                                         | X | X | X | X |
| CentOS Linux 7.6                                         | X | X | X | X |
| CentOS Linux 6                                           | X | X |   |   |
| CentOS Linux 6.5 以降                                        | X | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | x |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1+                                           |   |   |   | X |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           | X | X |   |   |
| Oracle Linux 6.4+                                        | X | X |   | X |
| Red Hat Enterprise Linux Server 8                        |   | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        | X | X | X |   |
| Red Hat Enterprise Linux Server 6.7 以降                     | X | X | X | X |
| SUSE Linux Enterprise Server 15                          | X | X |   |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14.04 LTS                                         | X | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>依存関係エージェントの Linux カーネル サポート
依存関係エージェントはカーネル レベルで動作するため、サポートもカーネルのバージョンに依存します。 次の表は、Linux OS のメジャーおよびマイナー リリースと依存関係エージェントでサポートされているカーネル バージョンの一覧です。

| Distribution | OS バージョン | カーネル バージョン |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 18.04   | 5.3.0-1020<br>5.0 (Azure で調整されたカーネルを含む)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15。* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4.12。* (Azure で調整されたカーネルを含む) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>次のステップ

各エージェントの詳細については、以下を参照してください。

- [Log Analytics エージェントの概要](log-analytics-agent.md)
- [Azure Diagnostics 拡張機能の概要](diagnostics-extension-overview.md)
- [Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](collect-custom-metrics-linux-telegraf.md)

