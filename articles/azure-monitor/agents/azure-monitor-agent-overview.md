---
title: Azure Monitor エージェントの概要
description: 仮想マシンのゲスト オペレーティング システムから監視データを収集する Azure Monitor エージェントの概要。
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/21/2021
ms.custom: references_regions
ms.openlocfilehash: c89625bb1ea7a9b2bee53468a09a48073e5516bf
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308657"
---
# <a name="azure-monitor-agent-overview"></a>Azure Monitor エージェントの概要
Azure Monitor エージェント (AMA) によって、Azure 仮想マシンのゲスト オペレーティング システムから監視データが収集され、それが Azure Monitor に配信されます。 この記事では、Azure Monitor エージェントの概要について説明します。また、そのインストール方法やデータ収集の構成方法などの情報が含まれます。

## <a name="relationship-to-other-agents"></a>他のエージェントとの関係
Azure Monitor エージェントは、仮想マシンからゲスト データを収集するために Azure Monitor によって現在使用されている次のレガシ エージェントに代わるものです ([既知のギャップを確認](../faq.yml))。

- [Log Analytics エージェント](./log-analytics-agent.md): データを Log Analytics ワークスペースに送信し、VM insights ソリューションと監視ソリューションをサポートします。
- [Diagnostics 拡張機能](./diagnostics-extension-overview.md): Azure Monitor メトリック (Windows のみ)、Azure Event Hubs、Azure Storage にデータを送信します。
- [Telegraf エージェント](../essentials/collect-custom-metrics-linux-telegraf.md): Azure Monitor メトリックにデータを送信します (Linux のみ)。

Azure Monitor エージェントでは、この機能が 1 つのエージェントに統合されるだけでなく、既存のエージェントに勝る次の利点があります。

- **監視の範囲:** さまざまな VM セットからのさまざまなデータ セットのコレクションを一元的に構成します。
- **Linux マルチホーム:** Linux VM から複数のワークスペースにデータを送信します。
- **Windows イベント フィルター:** XPATH クエリを使用して、収集される Windows イベントをフィルター処理します。
- **拡張機能管理の強化:** Azure Monitor エージェントでは、現在の Log Analytics エージェントの管理パックや Linux プラグインよりも透過的で制御可能な、拡張性を処理する新しい方法を使用しています。

### <a name="current-limitations"></a>現在の制限
既存のエージェントと比較して、この新しいエージェントはまだ完全には同等ではありません。
- **Log Analytics エージェント (MMA/OMS) との比較**
  - 一部の Log Analytics ソリューションは現在サポートされていません。 [サポート対象](#supported-services-and-features)を確認してください。
  - Azure Private Link はサポートされません。
  - ファイル ベースのログや IIS ログの収集はサポートされません。
- **Azure Diagnostics の拡張機能 (WAD/LAD) との比較:**
  - Event Hubs とストレージ アカウントは、送信先としてサポートされません。
  - ファイル ベースのログ、IIS ログ、ETW イベント、.NET イベント、クラッシュ ダンプの収集はサポートされていません。

### <a name="changes-in-data-collection"></a>データ収集の変更
既存のエージェントのデータ収集を定義する方法は、それぞれ明確に異なります。 各方法には、Azure Monitor エージェントによって対処される課題があります。

- Log Analytics エージェントでは、Log Analytics ワークスペースからその構成を取得します。 一元的に構成することは簡単ですが、異なる仮想マシンに個別の定義を定めるのは困難です。 データは、Log Analytics ワークスペースにのみ送信できます。
- 診断拡張機能では、仮想マシンごとに構成を指定します。 異なる仮想マシンに個別の定義を定めることは簡単ですが、一元的に管理することは困難です。 Azure Monitor メトリック、Azure Event Hubs、または Azure Storage にのみデータを送信できます。 Linux エージェントの場合、Azure Monitor メトリックにデータを送信するために、オープン ソースの Telegraf エージェントが必要です。

Azure Monitor エージェントでは、[データ収集ルール](data-collection-rule-overview.md)を使用して、各エージェントから収集するデータを構成します。 データ収集ルールにより、大規模な収集設定の管理が可能になる一方、コンピューターのサブセットの一意の範囲指定された構成も可能になります。 それらはワークスペースからも仮想マシンからも独立しているため、一度定義すれば複数のコンピューターや環境間で再利用できます。 「[Azure Monitor エージェント用のデータ収集の構成](data-collection-rule-azure-monitor-agent.md)」をご覧ください。

## <a name="should-i-switch-to-the-azure-monitor-agent"></a>Azure Monitor エージェントに切り替える必要はありますか?
Azure Monitor エージェントは [Azure Monitor のレガシ エージェント](agents-overview.md)の後継です。 現在のエージェントからこの新しいエージェントへ VM の移行を開始するには、次の要因を考慮します。

- **環境要件:** Azure Monitor エージェントでは現在、[これらのオペレーティング システム](./agents-overview.md#supported-operating-systems)をサポートしています。 この新しいエージェントでは高い確率で、将来のオペレーティング システムのバージョン、環境のサポート、およびネットワーク要件のサポートが提供される予定です。 
 
  ご使用の環境が Azure Monitor エージェントでサポートされているかどうかを評価してください。 そうでない場合は、現在のエージェントのままにする必要がある可能性があります。 Azure Monitor エージェントで現在の環境がサポートされている場合は、その環境への移行を検討してください。
- **現在と新規の機能要件:** Azure Monitor エージェントには、フィルター処理、スコープ、マルチホームなど、いくつかの新機能が導入されています。 ただし、カスタム ログ収集やすべてのソリューションとの統合など、他の機能については、まだ現在のエージェントと同等ではありません。 ([プレビュー段階のソリューションを参照してください](../faq.yml)。) 
 
  Azure Monitor のほとんどの新機能は、Azure Monitor エージェントでのみ使用できるようになります。 時間の経過と共に、新しいエージェントでのみ使用できる機能が増えます。 Azure Monitor エージェントに必要な機能があるかどうか、またこの新しいエージェントの他の重要な機能を利用するために、一時的に利用できなくてもよい機能があるかどうかを検討することをお勧めします。 
  
  必要なすべてのコア機能が Azure Monitor エージェントにある場合は、移行することを検討してください。 ユーザーにとって必須の重要な機能がある場合は、Azure Monitor エージェントが同等になるまで、現在のエージェントを引き続き使用してください。
- **やり直しの許容範囲:** デプロイ スクリプトやオンボード テンプレートなどのリソースを使用して新しい環境を設定している場合は、必要な作業量を評価します。 その設定に大量の作業が必要な場合は、一般提供されるようになった新しいエージェントを使用して、新しい環境を設定することを検討してください。 
 
  Azure Monitor の Log Analytics エージェントは 2024 年 8 月 31 日に廃止されます。 現在のエージェントは、非推奨になってから数年間サポートされます。

## <a name="supported-resource-types"></a>サポートされているリソースの種類
現在、Azure 仮想マシン、仮想マシン スケール セット、Azure Arc 対応サーバーがサポートされています。 Azure Kubernetes Service と他のコンピューティング リソースの種類は、現在サポートされていません。

## <a name="supported-regions"></a>サポートされているリージョン
Azure Monitor エージェントは、Log Analytics がサポートされているすべてのパブリック リージョンと、Azure Government および China クラウドで利用できます。 エアギャップ クラウドはまだサポートされていません。

## <a name="supported-operating-systems"></a>サポートされるオペレーティング システム
Azure Monitor エージェントで現在サポートされている Windows と Linux のオペレーティング システムのバージョンの一覧については、「[サポートされるオペレーティング システム](agents-overview.md#supported-operating-systems)」を参照してください。

## <a name="supported-services-and-features"></a>サポートされているサービスと機能
次の表に、他の Azure サービスに対する Azure Monitor エージェントの現在のサポートを示します。

| Azure サービス | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [Microsoft Defender for Cloud](../../security-center/security-center-introduction.md) | プライベート プレビュー | [サインアップ リンク](https://aka.ms/AMAgent) |
| [Microsoft Sentinel](../../sentinel/overview.md) | <ul><li>Windows イベント転送 (WEF): プライベート プレビュー</li><li>Windows セキュリティ イベント: [パブリック プレビュー](../../sentinel/connect-windows-security-events.md?tabs=AMA)</li></ul>  | <ul><li>[サインアップ リンク](https://aka.ms/AMAgent) </li><li>サインアップの必要はありません</li></ul> |

次の表に、Azure Monitor 機能に対する Azure Monitor エージェントの現在のサポートを示します。

| Azure Monitor 機能 | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [VM insights](../vm/vminsights-overview.md) | プライベート プレビュー  | [サインアップ リンク](https://aka.ms/amadcr-privatepreviews) |
| [プライベート リンクまたは AMPLS を使用して接続する](../logs/private-link-security.md) | AMA のプライベート プレビュー | [サインアップ リンク](https://aka.ms/amadcr-privatepreviews) |
| [VM insights のゲストの正常性](../vm/vminsights-health-overview.md) | パブリック プレビュー | 新しいエージェントでのみ使用可能 |
| [SQL Insights](../insights/sql-insights-overview.md) | パブリック プレビュー | 新しいエージェントでのみ使用可能 |

次の表に、Azure ソリューションに対する Azure Monitor エージェントの現在のサポートを示します。

| 解決策 | 現在のサポート | 詳細情報 |
|:---|:---|:---|
| [変更の追跡](../../automation/change-tracking/overview.md) | Microsoft Defender for Cloud プライベート プレビューでファイルの整合性の監視としてサポートされています。  | [サインアップ リンク](https://aka.ms/AMAgent) |
| [更新管理](../../automation/update-management/overview.md) | エージェントを必要としない Update Management v2 (プライベート プレビュー) を使用します。 | [サインアップ リンク](https://www.yammer.com/azureadvisors/threads/1064001355087872) |

## <a name="coexistence-with-other-agents"></a>他のエージェントとの共存
Azure Monitor エージェントは、既存のエージェントと共存させることができるため、評価または移行中にそれらの既存の機能を使用し続けることができます。 既存のソリューションのサポートにおける制限のため、この機能は重要です。 重複データの収集により、クエリ結果にずれが生じ、データ インジェストと保持により多くの料金が発生する可能性があるため、注意してください。 

たとえば、VM insights では、Log Analytics エージェントを使用して、パフォーマンス データが Log Analytics ワークスペースに送信されます。 また、エージェントから Windows のイベントと Syslog のイベントを収集するようにワークスペースが構成されている場合もあります。 Azure Monitor エージェントをインストールし、これらの同じイベントとパフォーマンス データに対してデータ収集ルールを作成した場合、データが重複することになります。

そのため、同じデータを両方のエージェントから収集しないようにしてください。 そうする場合は、別の収集先に行くようにしてください。

## <a name="costs"></a>コスト
Azure Monitor エージェントには料金はかかりませんが、取り込まれたデータについては料金が発生する場合があります。 Log Analytics のデータ収集と保持の詳細と、顧客メトリックについては、「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」を参照してください。

## <a name="data-sources-and-destinations"></a>データ ソースと収集先
次の表に、現在 Azure Monitor エージェントでデータ収集ルールを使用して収集できるデータの種類と、そのデータを送信できる場所を示します。 分析情報やソリューション、Azure Monitor エージェントを使用して他の種類のデータを収集するその他のソリューションの一覧については、「[Azure Monitor によって監視される内容](../monitor-reference.md)」を参照してください。

Azure Monitor エージェントでは、Azure Monitor メトリック (プレビュー)、または Azure Monitor ログをサポートする Log Analytics ワークスペースにデータが送信されます。

| データ ソース | 変換先 | Description |
|:---|:---|:---|
| パフォーマンス        | Azure Monitor メトリック (プレビュー)<sup>1</sup><br>Log Analytics ワークスペース | オペレーティング システムとワークロードのさまざまな側面のパフォーマンスを測定する数値 |
| Windows イベント ログ | Log Analytics ワークスペース | Windows イベント ログ システムに送信された情報 |
| syslog             | Log Analytics ワークスペース | Linux イベント ログ システムに送信される情報 |

<sup>1</sup> [ここをクリック](../essentials/metrics-custom-overview.md#quotas-and-limits)すると、Azure Monitor メトリックの使用に関するその他の制限を確認できます。 Linux では、Azure Monitor メトリックを唯一の宛先として使用する方法は、v.1.10.9.0 以降でサポートされています。 

## <a name="security"></a>セキュリティ
Azure Monitor エージェントにはキーは必要ありませんが、代わりに[システム割り当てマネージド ID](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity) が必要です。 エージェントをデプロイする前に、各仮想マシンで、システム割り当てマネージド ID を有効にしておく必要があります。

## <a name="networking"></a>ネットワーク
Azure Monitor エージェントは、Azure サービス タグに対応しています。 AzureMonitor と AzureResourceManager の両方のタグが必要です。 Azure Monitor エージェントは、Azure Monitor プライベート リンク スコープとはまだ連動しません。 インターネット経由で通信するためにマシンがプロキシ サーバーを介して接続されている場合は、次の要件を確認して必要とされるネットワーク構成を把握してください。

### <a name="proxy-configuration"></a>[プロキシ構成]

Windows や Linux 用の Azure Monitor エージェント拡張機能では、HTTPS プロトコルを使用することで、プロキシ サーバーまたは Log Analytics ゲートウェイのいずれかを経由して、Azure Monitor と通信できます。 Azure 仮想マシン、Azure 仮想マシン スケール セット、Azure Arc for servers に使用します。 次の手順で説明されているとおりに、その拡張機能の設定を構成に使用します。 匿名認証と、ユーザー名とパスワードを使用する基本認証の両方がサポートされています。

> [!IMPORTANT]
> プロキシの構成は、宛先としては [Azure Monitor メトリック (プレビュー)](../essentials/metrics-custom-overview.md) ではサポートされていません。 そのため、この宛先にメトリックを送信する場合は、プロキシなしでパブリック インターネットが使用されます。

1. このフローチャートを使用して、*setting* パラメーターと *protectedSetting* パラメーターの値を最初に決定します。

    ![拡張機能を有効にするときに setting パラメーターと protectedSetting パラメーターの値を決定するためのフローチャート。](media/azure-monitor-agent-overview/proxy-flowchart.png)

2. *setting* パラメーターと *protectedSetting* パラメーターの値が決定したら、PowerShell コマンドを使用して Azure Monitor エージェントをデプロイするときに、これらの追加パラメーターを指定します。 次の例は、Azure 仮想マシンの場合です。

    | パラメーター | 値 |
    |:---|:---|
    | 設定 | 前述のフローチャートの JSON オブジェクト。文字列に変換されます。 該当しない場合はスキップします。 例: {"proxy":{"mode":"application","address":"http://[address]:[port]","auth": false}} |
    | ProtectedSetting | 前述のフローチャートの JSON オブジェクト。文字列に変換されます。 該当しない場合はスキップします。 例: {"proxy":{"username": "[username]","password": "[password]"}} |

# <a name="windows-vm"></a>[Windows VM](#tab/PowerShellWindows)

```powershell
Set-AzVMExtension -ExtensionName AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.0 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-vm"></a>[Linux VM](#tab/PowerShellLinux)
```powershell
Set-AzVMExtension -ExtensionName AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -VMName <virtual-machine-name> -Location <location> -TypeHandlerVersion 1.5 -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="windows-arc-enabled-server"></a>[Windows Arc 対応サーバー](#tab/PowerShellWindowsArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorWindowsAgent -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

# <a name="linux-arc-enabled-server"></a>[Linux Arc 対応サーバー](#tab/PowerShellLinuxArc)
```powershell
New-AzConnectedMachineExtension -Name AzureMonitorLinuxAgent -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName <resource-group-name> -MachineName <arc-server-name> -Location <arc-server-location> -Setting <settingString> -ProtectedSetting <protectedSettingString>
```

---

## <a name="next-steps"></a>次のステップ

- Windows や Linux の仮想マシンに [Azure Monitor エージェント をインストール](azure-monitor-agent-install.md)します。
- [データ収集ルールを作成し](data-collection-rule-azure-monitor-agent.md)、エージェントからデータを収集して Azure Monitor に送信します。
