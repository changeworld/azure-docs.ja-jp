---
title: Azure ネイティブ サービスを使用して VM を監視および保護する
description: Microsoft Azure ネイティブ ツールを統合してデプロイし、Azure VMware Solution ワークロードを監視および管理する方法について説明します。
ms.topic: how-to
ms.date: 08/15/2021
ms.openlocfilehash: 8c95a74df7608aafbec09da9af94b0f82eb2ced3
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132315826"
---
# <a name="monitor-and-protect-vms-with-azure-native-services"></a>Azure ネイティブ サービスを使用して VM を監視および保護する

Microsoft Azure ネイティブ サービスを使用して、ハイブリッド環境 (Azure、Azure VMware Solution、オンプレミス) で仮想マシン (VM) を監視、管理、保護することができます。 この記事では、Azure VMware Solution のプライベート クラウドに Azure ネイティブ サービスを統合します。 また、これらのツールを使用して、ライフサイクル全体を通して VM を管理する方法も学習します。

Azure VMware Solution と統合できる Azure ネイティブ サービスには、次のものがあります。

- **Azure Arc** によって、Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。 [Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すると、Azure の "*外部*"、企業ネットワーク上、または他のクラウド プロバイダー上でホストされている Windows や Linux の物理サーバーと仮想マシンを管理できます。 [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) を使用して、Azure VMware Solution 環境でホストされている Kubernetes クラスターをアタッチできます。 

- **Azure Monitor** は、クラウドおよびオンプレミス環境からテレメトリを収集、分析して、対処します。 デプロイは必要ありません。  ゲスト オペレーティング システムのパフォーマンスを監視して Azure VMware Solution またはオンプレミスの VM のアプリケーションの依存関係を検出してマップすることができます。 Azure Monitor の Log Analytics ワークスペースにより、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 

   Azure Monitor で、さまざまな[ソースからデータを収集して監視および分析](../azure-monitor/agents/data-sources.md)し、さまざまな種類の[データを分析、視覚化、および警告](../azure-monitor/data-platform.md)することができます。 また、アラート ルールを作成することにより、高いリソースの使用率や不足しているパッチ、ディスク容量の不足、VM のハートビートなどの、環境内の問題を特定することもできます。 IT Service Management (ITSM) ツールにアラートを送信することで、検出されたイベントに対する自動応答を設定できます。 アラート検出の通知は、電子メールでも送信できます。

- **Microsoft Defender for Cloud** は、データ センターのセキュリティを強化し、クラウドやオンプレミスのハイブリッド ワークロード全体にわたって高度な脅威保護を提供します。 Azure VMware Solution VM の脆弱性を評価し、アラートを必要に応じて発生させ、これらを Azure Monitor へ解決のために転送します。 たとえば、オペレーティング システムのパッチの不足、セキュリティの構成の誤り、[エンドポイントの保護](../security-center/security-center-services.md)について評価します。 [Microsoft Defender for Cloud](azure-security-integration.md) では、セキュリティ ポリシーも定義できます。

- **Azure Update Management** が Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを Azure Automation のハイブリッド環境で管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。

- **Log Analytics ワークスペース** にログ データが格納されます。 各ワークスペースには、データを格納するための独自のデータ リポジトリと構成があります。 Log Analytics エージェントを介して Azure VMware Solution VM を監視できます。 Log Analytics ワークスペースに接続されているマシンでは、[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)を使用して、監視対象サーバーにインストールされているソフトウェア、Microsoft サービス、Windows のレジストリとファイル、および Linux デーモンの変更に関するデータが収集されます。 データが使用可能になると、処理のためにエージェントから Azure Monitor ログに送信されます。 Azure Monitor ログでは、受信したデータにロジックが適用され、記録されて分析可能になります。 Azure Arc 対応サーバー [VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、Log Analytics エージェントを VM でデプロイします。

## <a name="benefits"></a>メリット

- Azure ネイティブ サービスを使用して、ハイブリッド環境 (Azure、Azure VMware Solution、オンプレミス) の VM を管理できます。
- Azure、Azure VMware Solution、およびオンプレミスの VM に対する統合された監視と可視性が得られます。
- Azure Automation の Azure Update Management を使用して、Windows マシンと Linux マシンの両方のオペレーティング システムの更新プログラムを管理できます。
- Microsoft Defender for Cloud は、次のような高度な脅威保護を提供します。
  - ファイルの整合性の監視
  - ファイルレスのセキュリティ アラート
  - オペレーティング システムのパッチ評価
  - セキュリティの誤った構成の評価
  - エンドポイント保護の評価
- 新しい VM と既存の VM に対して、Azure Arc 対応サーバー VM 拡張機能のサポートを使用して、Log Analytics エージェントを簡単にデプロイできます。
- Azure Monitor の Log Analytics ワークスペースにより、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 データとログを 1 か所に収集し、そのデータを別の Azure ネイティブ サービスに提供します。
- Azure Monitor には次のような利点もあります。
  - シームレスな監視
  - インフラストラクチャの可視性の向上
  - インスタント通知
  - 自動解決
  - コスト効率

## <a name="topology"></a>トポロジ

図は、Azure VMware Solution VM の統合監視アーキテクチャを示しています。

:::image type="content" source="media/concepts/integrated-azure-monitoring-architecture.png" alt-text="統合された Azure 監視アーキテクチャを示す図。" border="false":::

Log Analytics エージェントを使用すると、Azure、Azure VMware Solution、オンプレミスの VM からのログ データの収集が可能になります。 ログ データは Azure Monitor ログに送信され、Log Analytics ワークスペースに格納されます。 Arc 対応サーバーの [VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、新しい VM や既存の VM のために Log Analytics エージェントをデプロイできます。

Log Analytics ワークスペースでログが収集されたら、Azure VMware Solution VM の脆弱性の状態を評価し、重大な脆弱性があればアラートを生成するように Defender for Cloud を使用して Log Analytics ワークスペースを構成できます。  たとえば、オペレーティング システムのパッチの不足、セキュリティの構成の誤り、[エンドポイントの保護](../security-center/security-center-services.md)について評価します。

Log Analytics ワークスペースで、Microsoft Sentinel によるアラートの検出、脅威の可視化、ハンティング、脅威対応を構成できます。 前の図では、Defender for Cloud は、Defender for Cloud コネクタを使用して Microsoft Sentinel に接続されています。 インシデントを作成し、他の脅威とマップするために、Defender for Cloud から Microsoft Sentinel に環境の脆弱性が転送されます。 スケジュールされたルールのクエリを作成して、望ましくないアクティビティを検出し、それをインシデントに変換することもできます。

## <a name="before-you-start"></a>開始する前に

Azure を初めて使用する場合、または前に説明したサービスに慣れていない場合は、次の記事を確認してください。

- [Automation アカウントの認証の概要](../automation/automation-security-overview.md)
- [Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)と [Azure Monitor](../azure-monitor/overview.md)
- Microsoft Defender for or Cloud の[プラン](../security-center/security-center-planning-and-operations-guide.md)と[サポートされるプラットフォーム](../security-center/security-center-os-coverage.md)
- [Azure Monitor for VMs の有効化の概要](../azure-monitor/vm/vminsights-enable-overview.md)
- 「[Azure Arc 対応サーバーとは](../azure-arc/servers/overview.md)」と「[Azure Arc 対応 Kubernetes とは](../azure-arc/kubernetes/overview.md)」
- [Update Management の概要](../automation/update-management/overview.md)



## <a name="enable-azure-update-management"></a>Azure Update Management を有効にする

Azure Automation の [Azure Update Management](../automation/update-management/overview.md) は、ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。

1. Azure Update Management に Log Analytics ワークスペースを追加するには、まず [Azure Automation アカウントを作成する](../automation/automation-create-standalone-account.md)必要があります。 

   >[!TIP]
   >[Azure Resource Manager (ARM) テンプレートを使用して、Automation アカウントを作成](../automation/quickstart-create-automation-account-template.md)することができます。 ARM テンプレートを使用すると、他のデプロイ方法より手順が減ります。

1. [Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)。 必要に応じて、[CLI](../azure-monitor/logs/resource-manager-workspace.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md)、または [Azure Resource Manager テンプレート](../azure-monitor/logs/resource-manager-workspace.md)を使用してワークスペースを作成することもできます。

1. [Automation アカウントから Update Management を有効にします](../automation/update-management/enable-from-automation-account.md)。 このプロセスでは、Log Analytics ワークスペースを Automation アカウントにリンクします。 
 
1. Update Management を有効にしたら、[VM に更新プログラムをデプロイして結果を確認する](../automation/update-management/deploy-updates.md)ことができます。 

## <a name="enable-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud を有効にする

Azure VMware Solution VM の脆弱性を評価し、アラートを必要に応じて発生させます。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。 詳しくは、[VM でサポートされる機能](../security-center/security-center-services.md)に関するページを参照してください。

Defender for Cloud には、以下を含む多くの機能が用意されています。

- ファイルの整合性の監視
- ファイルレス攻撃の検出
- オペレーティング システムのパッチ評価
- セキュリティの誤った構成の評価
- エンドポイント保護の評価

>[!NOTE]
>Microsoft Defender for Cloud はデプロイを必要としない事前構成済みのツールですが、Azure portal で有効にする必要があります。

1. [Defender for Cloud に Azure VMware Solution VM を追加します](azure-security-integration.md#add-azure-vmware-solution-vms-to-defender-for-cloud)。

2. [Microsoft Defender for Cloud を有効にします](../security-center/enable-azure-defender.md)。 Defender for Cloud は、潜在的なセキュリティの問題を見つけるために VM を評価します。 また、[セキュリティに関する推奨事項](../security-center/security-center-recommendations.md) も [概要] タブに表示されます。

3. Defender for Cloud で[セキュリティ ポリシーを定義します](../security-center/tutorial-security-policy.md)。

詳しくは、「[Microsoft Defender for Cloud と Azure VMware Solution を統合する](azure-security-integration.md)」を参照してください。

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーへの VM のオンボード

Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。  複数の Windows または Linux の VM に対して Azure Arc 対応サーバーを有効にすることの詳細については、「[ハイブリッド マシンを大規模に Azure に接続する](../azure-arc/servers/onboard-service-principal.md)」を参照してください。



## <a name="onboard-hybrid-kubernetes-clusters-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes を使用したハイブリッド Kubernetes クラスターのオンボード

Azure Arc 対応 Kubernetes を使用して、Azure VMware Solution 環境でホストされている Kubernetes クラスターをアタッチします。 詳細については、「[Azure Arc 対応オンボード サービス プリンシパルの作成](../azure-arc/kubernetes/create-onboarding-service-principal.md)」を参照してください。


## <a name="deploy-the-log-analytics-agent"></a>Log Analytics エージェントのデプロイ

Log Analytics エージェントを介して Azure VMware Solution VM を監視します。 Log Analytics ワークスペースに接続されているマシンでは、[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)を使用して、監視対象サーバーにインストールされているソフトウェア、Microsoft サービス、Windows のレジストリとファイル、および Linux デーモンの変更に関するデータが収集されます。 データが使用可能になると、処理のためにエージェントから Azure Monitor ログに送信されます。 Azure Monitor ログでは、受信したデータにロジックが適用され、記録されて分析可能になります。

[Azure Arc 対応サーバー VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、Log Analytics エージェントをデプロイします。




## <a name="enable-azure-monitor"></a>Azure Monitor を有効にする

さまざまな[ソースからデータを収集して監視および分析](../azure-monitor/agents/data-sources.md)し、さまざまな種類の[データを分析、視覚化、および警告](../azure-monitor/data-platform.md)することができます。 また、アラート ルールを作成することにより、高いリソースの使用率や不足しているパッチ、ディスク容量の不足、VM のハートビートなどの、環境内の問題を特定することもできます。 IT Service Management (ITSM) ツールにアラートを送信することで、検出されたイベントに対する自動応答を設定できます。 アラート検出の通知は、電子メールでも送信できます。

ゲスト オペレーティング システムのパフォーマンスを監視して Azure VMware Solution またはオンプレミス VM のアプリケーションの依存関係を検出してマップします。 Azure Monitor の Log Analytics ワークスペースにより、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 


1. [Azure Monitor ログのデプロイを設計します](../azure-monitor/logs/design-logs-deployment.md)

1. [Azure Monitor for VMs の有効化の概要](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Azure Monitor for VMs 用に Log Analytics ワークスペースを構成します](../azure-monitor/vm/vminsights-configure-workspace.md)。

1. アラート ルールを作成して、環境内の問題を特定します。

   - [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)。

   - [Azure Monitor を使用してログ アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-log.md)。

   - 自動アクションと通知を設定するための[アクション ルール](../azure-monitor/alerts/alerts-action-rules.md)。

   - [IT Service Management Connector を使用して Azure を ITSM ツールに接続する](../azure-monitor/alerts/itsmc-overview.md)。


## <a name="next-steps"></a>次のステップ

Azure VMware Solution のネットワークと相互接続の概念について学習したので、[Microsoft Defender for Cloud と Azure VMware Solution の統合](azure-security-integration.md)についての学習に進むことができます。
