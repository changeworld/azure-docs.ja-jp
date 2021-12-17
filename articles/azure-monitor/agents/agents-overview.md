---
title: Azure 監視エージェントの概要 | Microsoft Docs
description: この記事では、Azure またはハイブリッド環境にホストされている仮想マシンの監視をサポートする Azure エージェントについて概説します。
services: azure-monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.openlocfilehash: 92468fc96148dd22aab33e5cf7c4ff7a42c5ef65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308638"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor エージェントの概要

仮想マシンおよびその他のコンピューティング リソースでは、ゲスト オペレーティング システムとワークロードのパフォーマンスと可用性を測定するために必要な監視データを収集するエージェントが必要です。 この記事では、Azure Monitor で使用されるエージェントについて説明します。特定の環境の要件を満たす上で必要なエージェントを判断するのに役に立ちます。

> [!NOTE]
> Azure Monitor では最近、ゲスト オペレーティング システムの監視データを収集するために必要なすべての機能を提供する、新しいエージェントである Azure Monitor エージェントがリリースされました。 Azure Monitor と Log Analytics の統合によって複数のレガシ エージェントが存在するようになり、それぞれに独自の機能が備わっていますが、一部は重複しています。既存のすべてのエージェントの機能を統合し、追加の利点を得るために、新しいエージェントの使用をお勧めします。 [詳細情報](./azure-monitor-agent-overview.md)

## <a name="summary-of-agents"></a>エージェントの概要

次の表は、Windows と Linux の Azure Monitor エージェントを簡単に比較したものです。 それぞれの詳細については、以下のセクションで説明します。

### <a name="windows-agents"></a>Windows エージェント

| | Azure Monitor エージェント | 診断<br>拡張機能 (WAD) | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure<br>その他のクラウド (Azure Arc)<br>オンプレミス (Azure Arc)  | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | 
| **エージェントの要件**  | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | イベント ログ<br>パフォーマンス | イベント ログ<br>ETW イベント<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>.NET アプリ ログ<br>クラッシュ ダンプ<br>エージェント診断ログ | イベント ログ<br>パフォーマンス<br>ファイル ベース ログ<br>IIS ログ<br>分析情報とソリューション<br>その他のサービス | プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック<sup>1</sup> | Azure Storage<br>Azure Monitor メトリック<br>イベント ハブ | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | メトリックス エクスプローラー | VM insights<br>Log Analytics<br>Azure Automation<br>Microsoft Defender for Cloud<br>Microsoft Sentinel | VM insights<br>サービス マップ |

### <a name="linux-agents"></a>Linux エージェント

| | Azure Monitor エージェント | 診断<br>拡張機能 (LAD) | Telegraf<br>エージェント | Log Analytics<br>エージェント | 依存関係<br>エージェント |
|:---|:---|:---|:---|:---|:---|
| **サポートされている環境** | Azure<br>その他のクラウド (Azure Arc)<br>オンプレミス (Azure Arc) | Azure | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス | Azure<br>その他のクラウド<br>オンプレミス |
| **エージェントの要件**  | なし | なし | なし | なし | Log Analytics エージェントが必要 |
| **収集されるデータ** | syslog<br>パフォーマンス | syslog<br>パフォーマンス | パフォーマンス | syslog<br>パフォーマンス| プロセスの依存関係<br>ネットワーク接続のメトリック |
| **送信されるデータ** | Azure Monitor ログ<br>Azure Monitor メトリック<sup>1</sup> | Azure Storage<br>イベント ハブ | Azure Monitor メトリック | Azure Monitor ログ | Azure Monitor ログ<br>(Log Analytics エージェント経由) |
<<<<<<< HEAD
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | | メトリックス エクスプローラー | VM insights<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM insights<br>サービス マップ |

<sup>1</sup>現在の Linux 用 Azure Monitor エージェントには、Azure Monitor メトリックのみを送信先として使用することがサポートされない、という制限があります。
Azure Monitor ログと一緒に使用することで動作します。この制限は、次の拡張機能アップデートにて対処されます。


## <a name="azure-monitor-agent-preview"></a>Azure Monitor エージェント

[Azure Monitor エージェント](azure-monitor-agent-overview.md)は、Windows と Linux の両方のマシンで、Log Analytics エージェントおよび Telegraf エージェントを置き換えます。 Azure Monitor ログと Azure Monitor のメトリックの両方にデータを送信し、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用します。これにより、各エージェントのデータ コレクションと変換先をよりスケーラブルに構成する方法が提供されます。
=======
| **サービスと**<br>**features**<br>**サポート対象** | Log Analytics<br>メトリックス エクスプローラー | | メトリックス エクスプローラー | VM insights<br>Log Analytics<br>Azure Automation<br>Microsoft Defender for Cloud<br>Microsoft Sentinel | VM insights<br>サービス マップ |

<sup>1</sup> [ここをクリック](../essentials/metrics-custom-overview.md#quotas-and-limits)すると、Azure Monitor メトリックの使用に関するその他の制限を確認できます。 Linux では、Azure Monitor メトリックを唯一の宛先として使用する方法は、v.1.10.9.0 以降でサポートされています。 

## <a name="azure-monitor-agent"></a>Azure Monitor エージェント

[Azure Monitor エージェント](azure-monitor-agent-overview.md)は、Windows と Linux の両方のマシンで、Log Analytics エージェント、Azure 診断拡張機能、Telegraf エージェントの後継となります。 Azure Monitor ログと Azure Monitor のメトリックの両方にデータを送信し、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用します。これにより、各エージェントのデータ コレクションと変換先をよりスケーラブルに構成する方法が提供されます。
>>>>>>> repo_sync_working_branch

次のことを行う必要がある場合は、Azure Monitor エージェントを使用します。

- Azure、その他のクラウド、またはオンプレミスの任意のマシンからゲスト ログとメトリックを収集する。 (Azure の外部のマシンには [Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)が必要です。) 
<<<<<<< HEAD
- [データ収集ルール (DCR)](data-collection-rule-overview.md) を使用してデータ収集構成を一元管理し、Azure Resource Manger (ARM) のテンプレートやポリシーを使用して全体的な管理を行う
- Azure Monitor での分析のために、Azure Monitor ログと Azure Monitor メトリックにデータを送信する。
- Windows のイベント フィルタリングや、Windows と Linux のlogに対してマルチホーミングを活用する

Azure Monitor エージェントの制限事項は次のとおりです。

- 本番環境にて Log Analytics のソリューションを使用することはできません。 (プレビュー版でのみ使用可能、[サポート対象](https://docs.microsoft.com/ja-jp/azure/azure-monitor/agents/azure-monitor-agent-overview?tabs=PowerShellWindows#supported-services-and-features) を参照してください。)
- プライベート リンクを含むネットワークのシナリオはまだサポートされていません。
- カスタムログ (ファイル) や IIS ログ ファイルの収集はまだサポートされていません。
- Event Hubs やストレージ アカウントを送信先とすることはまだサポートされていません。
=======
- [データ収集ルール](./data-collection-rule-overview.md)を使用してデータ収集の構成を一元的に管理し、全体的な管理に Azure Resource Manager (ARM) テンプレートまたはポリシーを使用する。
- Azure Monitor での分析のために、Azure Monitor ログと Azure Monitor メトリック (プレビュー) にデータを送信する。 
- Windows と Linux でのログ用に Windows イベント フィルターまたはマルチホームを使用する。

<!--- Send data to Azure Storage for archiving.
- Send data to third-party tools using [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md).
- Manage the security of your machines using [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)  or [Microsoft Sentinel](../../sentinel/overview.md). (Available in private preview.)
- Use [VM insights](../vm/vminsights-overview.md) which allows you to monitor your machines at scale and monitors their processes and dependencies on other resources and external processes..  
- Manage the security of your machines using [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md)  or [Microsoft Sentinel](../../sentinel/overview.md).
- Use different [solutions](../monitor-reference.md#insights-and-curated-visualizations) to monitor a particular service or application. */
-->

Azure Monitor エージェントの制限事項には、以下が含まれます。

- 実稼働には Azure Monitor エージェントを使用できません (プレビューでのみ使用できます。[サポート対象に関する記事を参照してください](./azure-monitor-agent-overview.md#supported-services-and-features))。
- プライベート リンクが関係するネットワークのシナリオは、まだサポートされていません。 
- カスタム ログ (ファイル) や IIS ログ ファイルの収集は、まだサポートされていません。 
- Event Hubs とストレージ アカウントは、送信先としてまだサポートされていません。
>>>>>>> repo_sync_working_branch
- Hybrid Runbook Worker はサポートされていません。

## <a name="log-analytics-agent"></a>Log Analytics エージェント

[Log Analytics エージェント](./log-analytics-agent.md)は、Azure、他のクラウド プロバイダー、オンプレミス マシンの仮想マシンのゲスト オペレーティング システムとワークロードから、監視データを収集します。 データは、Log Analytics ワークスペースに送信されます。 Log Analytics エージェントは、System Center Operations Manager によって使用されるものと同じであり、エージェント コンピューターをマルチホーム化して管理グループおよび Azure Monitor との通信を同時に行います。 このエージェントは、Azure Monitor および Azure の他のサービスの特定の分析情報にも必要です。

> [!NOTE]
> Windows 用 Log Analytics エージェントは、多くの場合、Microsoft Monitoring Agent (MMA) と呼ばれます。 Linux 用 Log Analytics エージェントは、多くの場合、OMS エージェントと呼ばれます。

Log Analytics エージェントは、次のような場合に使用します。

* Azure の外部でホストされている Azure 仮想マシンまたはハイブリッド マシンから、ログとパフォーマンス データを収集する。
* データを Log Analytics ワークスペースに送信して、[ログ クエリ](../logs/log-query-overview.md)など、[Azure Monitor ログ](../logs/data-platform-logs.md)でサポートされている機能を活用する。
* マシンを大規模に監視し、そのプロセスや他のリソースおよび外部プロセスに対する依存関係を監視できる、[VM insights](../vm/vminsights-overview.md) を使用する。  
* [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) または [Microsoft Sentinel](../../sentinel/overview.md) を利用してコンピューターのセキュリティを管理します。
* [Azure Automation Update Management](../../automation/update-management/overview.md)、[Azure Automation State Configuration](../../automation/automation-dsc-overview.md)、または [Azure Automation Change Tracking と Inventory](../../automation/change-tracking/overview.md) を使用して、Azure および非 Azure マシンを包括的に管理する。
* さまざまな[ソリューション](../monitor-reference.md#insights-and-curated-visualizations)を使用して、特定のサービスまたはアプリケーションを監視する。

Log Analytics エージェントの制限事項は次のとおりです。

- Azure Monitor メトリック、Azure Storage、または Azure Event Hubs にデータを送信することはできません。
- 個々のエージェントに対して一意の監視定義を構成することは困難です。
- 各仮想マシンに固有の構成があるため、大規模な管理は困難です。

## <a name="azure-diagnostics-extension"></a>Azure Diagnostics 拡張機能

[Azure Diagnostics 拡張機能](./diagnostics-extension-overview.md)によって、Azure 仮想マシンおよびその他のコンピューティング リソースのゲスト オペレーティング システムとワークロードから監視データが収集されます。 データは主に Azure Storage に収集されますが、データ シンクを定義して、Azure Monitor メトリックや Azure Event Hubs などの他の送信先にデータを送信することもできます。

Azure Diagnostics 拡張機能は、次のような場合に使用します。

- アーカイブのため、または [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) などのツールで分析したりするために Azure Storage にデータを送信する。
- [Azure Monitor メトリック](../essentials/data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../alerts/alerts-metric-overview.md)や[自動スケーリング](../autoscale/autoscale-overview.md) (Windows のみ) などの機能を活用したりする。
- [Azure Event Hubs](./diagnostics-extension-stream-event-hubs.md) を使用して、データをサードパーティ製のツールに送信する。
- [ブート診断](/troubleshoot/azure/virtual-machines/boot-diagnostics)を収集して VM ブートの問題を調査する。

Azure Diagnostics 拡張機能の制限事項は次のとおりです。

- Azure リソースでのみ使用できます。
- Azure Monitor ログにデータを送信する機能が制限されています。

## <a name="telegraf-agent"></a>Telegraf エージェント

[InfluxData Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md)は、Linux コンピューターから Azure Monitor メトリックにパフォーマンス データを収集するために使用されます。

Telegraf エージェントは、次のような場合に使用します。

* [Azure Monitor メトリック](../essentials/data-platform-metrics.md)にデータを送信して、[メトリックス エクスプローラー](../essentials/metrics-getting-started.md)で分析したり、凖リアルタイムの[メトリック アラート](../alerts/alerts-metric-overview.md)や[自動スケーリング](../autoscale/autoscale-overview.md) (Linux のみ) などの機能を活用したりする。

## <a name="dependency-agent"></a>依存関係エージェント

依存関係エージェントは、マシンで実行されているプロセスおよび外部プロセスの依存関係に関する検出データを収集します。 

依存関係エージェントは、次のような場合に使用します。

* マップ機能の [VM insights](../vm/vminsights-overview.md) または [Service Map](../vm/service-map.md) ソリューションを使用する。

依存関係エージェントを使用する場合は、次の点を考慮してください。

- 依存関係エージェントを使用するには、Log Analytics エージェントを同じマシンにインストールする必要があります。
- Linux コンピューターの場合は、Azure 診断の拡張機能の前に Log Analytics エージェントをインストールする必要があります。
- Dependency Agent の Windows バージョンと Linux バージョンの両方で、データ収集はユーザー空間サービスとカーネル ドライバーを使用して行われます。 

## <a name="virtual-machine-extensions"></a>仮想マシン拡張機能

[Azure Monitor エージェント](./azure-monitor-agent-install.md#virtual-machine-extension-details)は、仮想マシンの拡張機能としてのみ使用できます。 [Windows](../../virtual-machines/extensions/oms-windows.md) および [Linux](../../virtual-machines/extensions/oms-linux.md) 用の Log Analytics 拡張機能は、Azure 仮想マシンに Log Analytics エージェントをインストールします。 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) および [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 用の Azure Monitor 依存関係拡張機能は、Azure 仮想マシンに依存関係エージェントをインストールします。 これらは上記と同じエージェントですが、これらは[仮想マシン拡張機能](../../virtual-machines/extensions/overview.md)を使用して管理できます。 可能な場合は、拡張機能を使用してエージェントをインストールおよび管理する必要があります。

ハイブリッド マシンでは、[Azure Arc 対応サーバー](../../azure-arc/servers/manage-vm-extensions.md)を使用して、Azure Monitor エージェント、Log Analytics および Azure Monitor Dependency VM 拡張機能をデプロイします。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム

次の表は、Azure Monitor エージェントでサポートされているオペレーティング システムの一覧です。 エージェント固有の考慮事項とインストール プロセスについては、各エージェントのドキュメントをご覧ください。 Telegraf でサポートされているオペレーティング システムについては、Telegraf のドキュメントをご覧ください。 オペレーティング システムはすべて x64 と見なされます。 x86 はどのオペレーティング システムでもサポートされていません。

### <a name="windows"></a>Windows

| オペレーティング システム | Azure Monitor エージェント | Log Analytics エージェント | 依存関係エージェント | 診断拡張機能 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2022                                      | X |   |   |   |
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2019 Core                                 | X |   |   |   |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 | X |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2 SP1                               | X | X | X | X |
| Windows Server 2008 R2                                   |   |   | X | X |
| Windows Server 2008 SP2                                   |   | X |  |  |
| Windows 10 Enterprise<br>(マルチセッションを含む) および Pro<br>(サーバー シナリオのみ<sup>1</sup>)  | X | X | X | X |
| Windows 8 Enterprise および Pro<br>(サーバー シナリオのみ<sup>1</sup>)  |   | X | X |   |
| Windows 7 SP1<br>(サーバー シナリオのみ<sup>1</sup>)                 |   | X | X |   |
| Azure Stack HCI                                          |   | X |   |   |

<sup>1</sup> サーバー ハードウェアで OS が動作。つまり、常に接続されて電源が入っており、その他のワークロード (PC、Office、ブラウザーなど) を実行していないマシン

### <a name="linux"></a>Linux

| オペレーティング システム | Azure Monitor エージェント <sup>1</sup> | Log Analytics エージェント <sup>1</sup> | 依存関係エージェント | 診断拡張機能 <sup>2</sup>| 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                        |   | X |   |   |
| Amazon Linux 2                                              |   | X |   |   |
| CentOS Linux 8                                              | X <sup>3</sup> | X | X |   |
| CentOS Linux 7                                              | X | X | X | X |
| CentOS Linux 6                                              |   | X |   |   |
| CentOS Linux 6.5 以降                                           |   | X | X | X |
| Debian 10 <sup>1</sup>                                      | X |   |   |   |
| Debian 9                                                    | X | X | x | X |
| Debian 8                                                    |   | X | X |   |
| Debian 7                                                    |   |   |   | X |
| OpenSUSE 13.1+                                              |   |   |   | X |
| Oracle Linux 8                                              | X <sup>3</sup> | X |   |   |
| Oracle Linux 7                                              | X | X |   | X |
| Oracle Linux 6                                              |   | X |   |   |
| Oracle Linux 6.4+                                           |   | X |   | X |
| Red Hat Enterprise Linux Server 8.1, 8.2, 8.3, 8.4          | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 8                           | X <sup>3</sup> | X | X |   |
| Red Hat Enterprise Linux Server 7                           | X | X | X | X |
| Red Hat Enterprise Linux Server 6                           |   | X | X |   |
| Red Hat Enterprise Linux Server 6.7 以降                        |   | X | X | X |
| SUSE Linux Enterprise Server 15.2                           | X <sup>3</sup> |   |   |   |
| SUSE Linux Enterprise Server 15.1                           | X <sup>3</sup> | X |   |   |
| SUSE Linux Enterprise Server 15 SP1                         | X | X | X |   |
| SUSE Linux Enterprise Server 15                             | X | X | X |   |
| SUSE Linux Enterprise Server 12 SP5                         | X | X | X | X |
| SUSE Linux Enterprise Server 12                             | X | X | X | X |
| Ubuntu 20.04 LTS                                            | X | X | X | X |
| Ubuntu 18.04 LTS                                            | X | X | X | X |
| Ubuntu 16.04 LTS                                            | X | X | X | X |
| Ubuntu 14.04 LTS                                            |   | X |   | X |

<sup>1</sup> マシンに Python (2 または 3) がインストールされている必要があります。

<sup>3</sup> 1.9.0 より前のバージョンでの Syslog イベント収集に関する既知の問題。

#### <a name="dependency-agent-linux-kernel-support"></a>依存関係エージェントの Linux カーネル サポート

依存関係エージェントはカーネル レベルで動作するため、サポートもカーネルのバージョンに依存します。 次の表は、Linux OS のメジャーおよびマイナー リリースと依存関係エージェントでサポートされているカーネル バージョンの一覧です。

| Distribution | OS バージョン | カーネル バージョン |
|:---|:---|:---|
|  Red Hat Linux 8   | 8.4     | 4.18.0-305.\*el8.x86_64, 4.18.0-305.\*el8_4.x86_64 |
|                    | 8.3     |  4.18.0-240.\*el8_3.x86_64 |
|                    | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
|  Red Hat Linux 7   | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
|                    | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOS Linux 8     | 8.4     | 4.18.0-305.\*el8.x86_64, 4.18.0-305.\*el8_4.x86_64 |
|                    | 8.3     | 4.18.0-240.\*el8_3.x86_64 |
|                    | 8.2     | 4.18.0-193.\*el8_2.x86_64 |
|                    | 8.1     | 4.18.0-147.\*el8_1.x86_64 |
|                    | 8.0     | 4.18.0-80.\*el8.x86_64<br>4.18.0-80.\*el8_0.x86_64 |
| CentOS Linux 7     | 7.9     | 3.10.0-1160 |
|                    | 7.8     | 3.10.0-1136 |
|                    | 7.7     | 3.10.0-1062 |
| CentOS Linux 6     | 6.10    | 2.6.32-754.3.5<br>2.6.32-696.30.1 |
|                    | 6.9     | 2.6.32-696.30.1<br>2.6.32-696.18.7 |
| Ubuntu Server      | 20.04   | 5.4\* |
|                    | 18.04   | 5.3.0-1020<br>5.0 (Azure で調整されたカーネルを含む)<br>4.18 *<br>4.15* |
|                    | 16.04.3 | 4.15.\* |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP5     | 4.12.14-122.\*-default, 4.12.14-16.\*-azure|
|                                 | 12 SP4 | 4.12.\* (Azure で調整されたカーネルを含む) |
|                                 | 12 SP3 | 4.4.\* |
|                                 | 12 SP2 | 4.4.\* |
| SUSE Linux 15 Enterprise Server | 15 SP1 | 4.12.14-197.\*-default, 4.12.14-8.\*-azure |
|                                 | 15     | 4.12.14-150.\*-default |
| Debian                          | 9      | 4.9  | 

## <a name="next-steps"></a>次のステップ

各エージェントの詳細については、以下を参照してください。

- [Azure Monitor エージェントの概要](./azure-monitor-agent-overview.md)
- [Log Analytics エージェントの概要](./log-analytics-agent.md)
- [Azure Diagnostics 拡張機能の概要](./diagnostics-extension-overview.md)
- [Linux VM のカスタム メトリックを InfluxData Telegraf エージェントを使用して収集する](../essentials/collect-custom-metrics-linux-telegraf.md)
