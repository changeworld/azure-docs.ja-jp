---
title: Azure Monitor エージェントの概要
description: 仮想マシンのゲスト オペレーティング システムから監視データを収集する Azure Monitor エージェント (AMA) の概要。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/22/2021
ms.custom: references_regions
ms.openlocfilehash: 7c12fff502d8c4b68470370e0a5eede1dd3866a9
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2021
ms.locfileid: "114605370"
---
# <a name="azure-monitor-agent-overview"></a>Azure Monitor エージェントの概要
Azure Monitor エージェント (AMA) によって、Azure 仮想マシンのゲスト オペレーティング システムから監視データが収集され、それが Azure Monitor に配信されます。 この記事では、Azure Monitor エージェントのインストール方法やデータ収集の構成方法など、Azure Monitor エージェントの概要について説明します。

## <a name="relationship-to-other-agents"></a>他のエージェントとの関係
Azure Monitor エージェントは、仮想マシンからゲスト データが収集するために Azure Monitor によって現在使用されている次のレガシ エージェントに代わるものです ([既知のギャップを確認](/azure/azure-monitor/faq#is-the-new-azure-monitor-agent-at-parity-with-existing-agents))。

- [Log Analytics エージェント](./log-analytics-agent.md) - データが Log Analytics ワークスペースに送信され、VM insights ソリューションと監視ソリューションがサポートされます。
- [Diagnostics 拡張機能](./diagnostics-extension-overview.md) - Azure Monitor メトリック (Windows のみ)、Azure Event Hubs、および Azure Storage にデータが送信されます。
- [Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md) - Azure Monitor メトリックにデータが送信されます (Linux のみ)。

Azure Monitor エージェントでは、この機能が 1 つのエージェントに統合されるだけでなく、既存のエージェントに勝る次の利点があります。

- 監視の範囲。 さまざまな VM セットからのさまざまなデータ セットのコレクションが一元的に構成されます。  
- Linux マルチホーム:Linux VM から複数のワークスペースにデータが送信されます。
- Windows イベント フィルター:XPATH クエリを使用して、収集される Windows イベントがフィルター処理されます。
- 拡張機能管理の強化:Azure Monitor エージェントでは、現在の Log Analytics エージェントの管理パックや Linux プラグインよりも透過的で制御可能な、拡張性を処理する新しい方法を使用しています。

### <a name="changes-in-data-collection"></a>データ収集の変更
既存のエージェントのデータ収集を定義する方法は、それぞれ明確に異なっており、それぞれ Azure Monitor エージェントによって対処される課題を抱えています。

- Log Analytics エージェントでは、Log Analytics ワークスペースからその構成が取得されます。 一元的に構成することは簡単ですが、異なる仮想マシンに個別の定義を定めるのは困難です。 データは、Log Analytics ワークスペースにのみ送信できます。

- 診断拡張機能では、仮想マシンごとに構成を指定します。 異なる仮想マシンに個別の定義を定めることは簡単ですが、一元的に管理することは困難です。 Azure Monitor メトリック、Azure Event Hubs、または Azure Storage にのみデータを送信できます。 Linux エージェントの場合、Azure Monitor メトリックにデータを送信するために、オープン ソースの Telegraf エージェントが必要です。

Azure Monitor エージェントでは、[データ収集ルール (DCR)](data-collection-rule-overview.md) を使用して、各エージェントから収集するデータが構成されます。 データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースから独立し、仮想マシンからも独立しているため、一度定義すると、コンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成](data-collection-rule-azure-monitor-agent.md)」をご覧ください。

## <a name="should-i-switch-to-azure-monitor-agent"></a>Azure Monitor エージェントに切り替える必要はありますか?
Azure Monitor エージェントは [Azure Monitor のレガシ エージェント](agents-overview.md)の後継です。次の要因を考慮した上で、現在のエージェントからこの新しいエージェントへ、VM の移行を開始できます。

- **環境要件。** Azure Monitor エージェントは現在、[こちらのオペレーティング システム](./agents-overview.md#supported-operating-systems)をサポートしています。 この新しいエージェントでは高い確率で、将来のオペレーティング システムのバージョン、環境のサポート、およびネットワーク要件のサポートが提供される予定です。 お使いの環境が Azure Monitor エージェントでサポートされているかどうかを評価することをお勧めします。 そうでない場合は、現在のエージェントのままにする必要がある可能性があります。 Azure Monitor エージェントが現在の環境をサポートしている場合は、その環境への移行を検討することをお勧めします。
- **現在と新規の機能要件。** Azure Monitor エージェントでは、フィルター処理、スコープ、マルチホームなど、いくつかの新機能を導入していますが、カスタム ログ収集や各ソリューションとの統合など、他の機能については、まだ現在のエージェントと同等ではありません ([プレビューのソリューションを参照](/azure/azure-monitor/faq#which-log-analytics-solutions-are-supported-on-the-new-azure-monitor-agent))。 Azure Monitor のほとんどの新機能は、Azure Monitor エージェントでのみ使用できるようになるため、時間の経過と共に、新しいエージェントでのみ使用できる機能が増えます。 Azure Monitor エージェントに実際に必要な機能があるかどうか、またこの新しいエージェントの他の重要な機能を利用するために、一時的に利用できなくてもよい機能があるかどうかを検討することをお勧めします。 必要なすべてのコア機能が Azure Monitor エージェントにある場合は、移行することを検討してください。 必須の重要な機能がある場合は、Azure Monitor エージェントが同等になるまで、現在のエージェントを引き続き使用します。
- **やり直しの許容範囲。** デプロイ スクリプトやオンボード テンプレートなどのリソースを使用して新しい環境を設定する場合は、必要な作業量を評価します。 大量の作業が必要な場合は、現在一般提供されている新しいエージェントを使用して、新しい環境を設定することを検討してください。 Log Analytics エージェントが非推奨となる日付は、2021 年 8 月に発表される予定です。 非推奨化が始まってから数年間は、現在のエージェントはサポートされます。

## <a name="supported-resource-types"></a>サポートされているリソースの種類
現在、Azure 仮想マシン、仮想マシン スケール セット、Azure Arc 対応サーバーがサポートされています。 Azure Kubernetes Service と他のコンピューティング リソースの種類は、現在サポートされていません。


## <a name="supported-regions"></a>サポートされているリージョン
Azure Monitor エージェントは、Log Analytics をサポートしているすべてのパブリック リージョンで使用できます。 政府機関向けのリージョンおよびクラウドは現在サポートされていません。
## <a name="supported-services-and-features"></a>サポートされているサービスと機能
次の表に、他の Azure サービスに対する Azure Monitor エージェントの現在のサポートを示します。

| Azure サービス | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [Azure Security Center](../../security-center/security-center-introduction.md) | プライベート プレビュー | [サインアップ リンク](https://aka.ms/AMAgent) |
| [Azure Sentinel](../../sentinel/overview.md) | プライベート プレビュー | [サインアップ リンク](https://aka.ms/AMAgent) |


次の表に、Azure Monitor 機能に対する Azure Monitor エージェントの現在のサポートを示します。

| Azure Monitor 機能 | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [VM Insights](../vm/vminsights-overview.md) | プライベート プレビュー  | [サインアップ リンク](https://forms.office.com/r/jmyE821tTy) |
| [VM insights のゲストの正常性](../vm/vminsights-health-overview.md) | パブリック プレビュー | 新しいエージェントでのみ使用可能 |
| [SQL Insights](../insights/sql-insights-overview.md) | パブリック プレビュー | 新しいエージェントでのみ使用可能 |

次の表に、Azure ソリューションに対する Azure Monitor エージェントの現在のサポートを示します。

| 解決策 | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [変更の追跡](../../automation/change-tracking/overview.md) | Azure Security Center プライベート プレビューでファイルの整合性の監視 (FIM) としてサポートされています。  | [サインアップ リンク](https://aka.ms/AMAgent) |
| [更新管理](../../automation/update-management/overview.md) | エージェントを必要としない Update Management v2 (プライベート プレビュー) を使用します。 | [サインアップ リンク](https://www.yammer.com/azureadvisors/threads/1064001355087872) |



## <a name="coexistence-with-other-agents"></a>他のエージェントとの共存
Azure Monitor エージェントは、既存のエージェントと共存させることができるため、評価または移行中にそれらの既存の機能を使用し続けることができます。 これは、既存のソリューションのサポートにおける制限のため、特に重要です。 ただし、重複データの収集では注意する必要があります。これにより、クエリ結果にずれが生じ、データ インジェストと保持に追加料金が発生する可能性があるためです。 

たとえば、VM Insights では、Log Analytics エージェントを使用して、パフォーマンス データが Log Analytics ワークスペースに送信されます。 また、エージェントから Windows イベントと Syslog イベントを収集するようにワークスペースを構成している場合もあります。 Azure Monitor エージェントをインストールし、これらの同じイベントとパフォーマンス データに対してデータ収集ルールを作成した場合、データが重複することになります。

そのため、同じデータを両方のエージェントから収集しないようにしてください。 そうする場合は、別の収集先に行くようにしてください。


## <a name="costs"></a>コスト
Azure Monitor エージェントには料金はかかりませんが、取り込まれたデータの料金が発生する場合があります。 Log Analytics のデータ収集と保持の詳細についてと顧客メトリックについては、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="data-sources-and-destinations"></a>データ ソースと収集先
次の表に、現在 Azure Monitor エージェントでデータ収集ルールを使用して収集できるデータの種類と、そのデータを送信できる場所を示します。 分析情報、ソリューション、および Azure Monitor エージェントを使用して他の種類のデータを収集するその他のソリューションのリストについては、「[Azure Monitor で何が監視されていますか?](../monitor-reference.md)」 を参照してください。


Azure Monitor エージェントでは、Azure Monitor メトリック、または Azure Monitor ログをサポートする Log Analytics ワークスペースにデータが送信されます。

| Data Source | 変換先 | Description |
|:---|:---|:---|
| パフォーマンス        | Azure Monitor メトリック<sup>1</sup><br>Log Analytics ワークスペース | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値。 |
| Windows イベント ログ | Log Analytics ワークスペース | Windows イベント ログ システムに送信された情報。 |
| syslog             | Log Analytics ワークスペース | Linux イベント ログ システムに送信される情報。 |

<sup>1</sup> 現在 Azure Monitor Agent for Linux には、Azure Monitor メトリックを "*唯一の*" 収集先として使用する方法はサポートされていないという制限があります。 Azure Monitor ログと一緒に使用すると、うまくいきます。 この制限は、次の拡張機能の更新プログラムで対処されます。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure Monitor エージェントで現在サポートされている Windows および Linux オペレーティング システムのバージョンの一覧については、「[サポートされるオペレーティング システム](agents-overview.md#supported-operating-systems)」を参照してください。



## <a name="security"></a>セキュリティ
Azure Monitor エージェントにはキーは必要ありませんが、代わりに[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) が必要です。 エージェントをデプロイする前に、各仮想マシンで、システム割り当てマネージド ID を有効にしている必要があります。

## <a name="networking"></a>ネットワーキング
Azure Monitor エージェントでは Azure サービス タグ (AzureMonitor と AzureResourceManager の両方のタグが必要) がサポートされますが、Azure Monitor プライベート リンク スコープとはまだ連動していません。 インターネット経由で通信するためにマシンがプロキシ サーバーを介して接続する場合、以下の要件を確認して必要なネットワーク構成を把握してください。

### <a name="proxy-configuration"></a>[プロキシ構成]

Windows および Linux 用の Azure Monitor エージェント拡張機能は、HTTPS プロトコルを使用して、プロキシ サーバーまたは Log Analytics ゲートウェイを介して Azure Monitor と通信できます (Azure 仮想マシンの場合は、Azure Virtual Machine Scale Sets および Azure Arc for servers)。 これは、下で説明するように拡張機能の設定を使用して構成され、匿名と基本の認証 (ユーザー名/パスワード) の両方がサポートされています。  

1. こちらの簡単なフローチャートを使用して、*setting* と *protectedSetting* のパラメーターの値を最初に決定します。

   ![拡張機能を有効にするときに setting と protectedSetting のパラメーターの値を決定するフローチャート](media/azure-monitor-agent-overview/proxy-flowchart.png)


2. *setting* と *protectedSetting* のパラメーターの値が決定したら、PowerShell コマンドを使用して Azure Monitor エージェントをデプロイするときに、こちらの追加パラメーターを指定します (Azure 仮想マシン場合の例を下に示します)。

    | パラメーター | 値 |
    |:---|:---|
    | SettingString | 上記のフローチャートの JSON オブジェクト。文字列に変換されます。該当しない場合はスキップします。 例: {"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}} |
    | ProtectedSettingString | 上記のフローチャートの JSON オブジェクト。文字列に変換されます。該当しない場合はスキップします。 例: {"proxy":{"username": "[username]","password": "[password]"}} |


# <a name="windows"></a>[Windows](#tab/PowerShellWindows)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

# <a name="linux"></a>[Linux](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -SettingString <settingString> -ProtectedSettingString <protectedSettingString>
```

---

## <a name="next-steps"></a>次のステップ

- Windows および Linux の仮想マシンに [Azure Monitor エージェント をインストール](azure-monitor-agent-install.md)します。
- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。
