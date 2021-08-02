---
title: Azure ネイティブ サービスを統合してデプロイする
description: Microsoft Azure ネイティブ ツールを統合してデプロイし、Azure VMware Solution ワークロードを監視および管理する方法について説明します。
ms.topic: how-to
ms.date: 06/14/2021
ms.openlocfilehash: e7af5617ee62f451f57daf806f70c7c433e40612
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758216"
---
# <a name="integrate-and-deploy-azure-native-services"></a>Azure ネイティブ サービスを統合してデプロイする

Microsoft Azure ネイティブ サービスを使用して、ハイブリッド環境 (Azure、Azure VMware Solution、オンプレミス) で仮想マシン (VM) を監視、管理、保護することができます。 詳しくは、[VM でサポートされる機能](../security-center/security-center-services.md)に関するページを参照してください。

Azure VMware ソリューションと統合できる Azure ネイティブ サービスには、次のものがあります。

- **Log Analytics ワークスペース:** 各ワークスペースには、ログ データを格納するための独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 新しい VM と既存の VM に対して、Azure Arc 対応サーバー VM 拡張機能のサポートを使用して、Log Analytics エージェントを簡単にデプロイできます。 
- **Azure Security Center:** データ センターのセキュリティを強化し、クラウドやオンプレミスのハイブリッド ワークロード全体にわたる高度な脅威保護を提供する、統合されたインフラストラクチャ セキュリティ管理システム。 
- **Azure Sentinel** は、クラウドネイティブのセキュリティ情報イベント管理 (SIEM) ソリューションです。 環境全体にわたって、セキュリティ分析、アラート検出、自動化された脅威対応を提供します。 Azure Sentinel は、Log Analytics ワークスペースの上に構築されています。
- **Azure Arc:** Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。 
- **Azure Update Management:** ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。
- **Azure Monitor:** クラウドおよびオンプレミス環境からのテレメトリを収集、分析して対応する、包括的なソリューションです。 デプロイは必要ありません。 

この記事では、Azure VMware Solution のプライベート クラウドに Azure ネイティブ サービスを統合します。 また、これらのツールを使用して、ライフサイクル全体を通して VM を管理する方法も学習します。 


## <a name="enable-azure-update-management"></a>Azure Update Management を有効にする

Azure Automation の [Azure Update Management](../automation/update-management/overview.md) は、ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。

1. [Azure Automation アカウントを作成します](../automation/automation-create-standalone-account.md)。 

   >[!TIP]
   >[Azure Resource Manager (ARM) テンプレートを使用して、Automation アカウントを作成](../automation/quickstart-create-automation-account-template.md)することができます。 ARM テンプレートを使用すると、他のデプロイ方法より手順が減ります。

1. [Automation アカウントから Update Management を有効にします](../automation/update-management/enable-from-automation-account.md)。  これにより、Log Analytics ワークスペースが Automation アカウントにリンクされます。 また、Update Management で Azure VM と Azure 以外の VM も使用できるようになります。

   - ワークスペースがある場合は、 **[更新管理]** を選択します。 Log Analytics ワークスペースと Automation アカウントを選択し、 **[有効にする]** を選択します。 セットアップが完了するまでに最大 15 分かかります。

   - 新規の Log Analytics ワークスペースを作成する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください [CLI](../azure-monitor/logs/quick-create-workspace-cli.md)、[PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md)、または [Azure Resource Manager テンプレート](../azure-monitor/logs/resource-manager-workspace.md)を使用してワークスペースを作成することもできます。
 
1. Update Management を有効にしたら、[VM に更新プログラムをデプロイして結果を確認する](../automation/update-management/deploy-updates.md)ことができます。 


## <a name="enable-azure-security-center"></a>Azure Security Center を有効にする

Azure Security Center は、Azure VMware Solution とオンプレミス仮想マシン (VM) 全体で高度な脅威防止を実現します。 Azure VMware Solution VM の脆弱性を評価し、必要に応じてアラートを生成します。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。 

Azure Security Center には、次のような多くの機能が用意されています。
- ファイルの整合性の監視
- ファイルレス攻撃の検出
- オペレーティング システムのパッチ評価 
- セキュリティの誤った構成の評価
- エンドポイント保護の評価

>[!NOTE]
>Azure Security Center はデプロイを必要としない事前構成済みのツールですが、Azure portal で有効にする必要があります。 

Azure Security Center を有効にするには、[Azure Security Center と Azure VMware Solution の統合](azure-security-integration.md)に関するページを参照してください。

## <a name="onboard-vms-to-azure-arc-enabled-servers"></a>Azure Arc 対応サーバーへの VM のオンボード

Azure Arc によって、Azure VMware Solution やオンプレミスを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。  [Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すると、Azure の "*外部*"、企業ネットワーク上、または他のクラウド プロバイダー上でホストされている Windows や Linux の物理サーバーと仮想マシンを管理できます。

複数の Windows または Linux の VM に対して Azure Arc 対応サーバーを有効にすることの詳細については、「[ハイブリッド マシンを大規模に Azure に接続する](../azure-arc/servers/onboard-service-principal.md)」を参照してください。

## <a name="onboard-hybrid-kubernetes-clusters-with-arc-enabled-kubernetes"></a>Arc 対応 Kubernetes を使用したハイブリッド Kubernetes クラスターのオンボード

[Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) を使用して、Azure VMware Solution 環境でホストされている Kubernetes クラスターをアタッチできます。 

詳細については、「[Azure Arc 対応オンボード サービス プリンシパルの作成](../azure-arc/kubernetes/create-onboarding-service-principal.md)」を参照してください。

## <a name="deploy-the-log-analytics-agent"></a>Log Analytics エージェントのデプロイ

Log Analytics エージェントを介して Azure VMware Solution VM を監視できます。 Log Analytics ワークスペースに接続されているマシンでは、[Log Analytics エージェント](../azure-monitor/agents/log-analytics-agent.md)を使用して、監視対象サーバーにインストールされているソフトウェア、Microsoft サービス、Windows のレジストリとファイル、および Linux デーモンの変更に関するデータが収集されます。 データが使用可能になると、処理のためにエージェントから Azure Monitor ログに送信されます。 Azure Monitor ログでは、受信したデータにロジックが適用され、記録されて分析可能になります。

[Azure Arc 対応サーバー VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、Log Analytics エージェントをデプロイします。

## <a name="enable-azure-monitor"></a>Azure Monitor を有効にする

[Azure Monitor](../azure-monitor/overview.md) は、クラウドおよびオンプレミス環境からのテレメトリを収集、分析して対応する、包括的なソリューションです。 Azure Monitor には次のような利点もあります。 

   - シームレスな監視 

   - インフラストラクチャの可視性の向上 

   - インスタント通知 

   - 自動解決 

   - コスト効率 

さまざまなソースからデータを収集して、監視および分析できます。 詳細については、「[Azure Monitor で使用する監視データのソース](../azure-monitor/agents/data-sources.md)」を参照してください。  分析や視覚化、アラート用に、さまざまな種類のデータを収集することもできます。 詳細については、「[Azure Monitor データ プラットフォーム](../azure-monitor/data-platform.md)」を参照してください。 

ゲスト オペレーティング システムのパフォーマンスを監視し、Azure VMware Solution またはオンプレミスの VM のアプリケーションの依存関係を検出してマップすることができます。 また、アラート ルールを作成することにより、高いリソースの使用率や不足しているパッチ、ディスク容量の不足、VM のハートビートなどの、環境内の問題を特定することもできます。 IT Service Management (ITSM) ツールにアラートを送信することで、検出されたイベントに対する自動応答を設定できます。 アラート検出の通知は、電子メールでも送信できます。


1. [Azure Monitor ログのデプロイを設計します](../azure-monitor/logs/design-logs-deployment.md)

1. [Azure Monitor for VMs の有効化の概要](../azure-monitor/vm/vminsights-enable-overview.md)

1. [Azure Monitor for VMs 用に Log Analytics ワークスペースを構成します](../azure-monitor/vm/vminsights-configure-workspace.md)。

1. アラート ルールを作成して、環境内の問題を特定します。
   - [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-metric.md)。
   - [Azure Monitor を使用してログ アラートを作成、表示、管理する](../azure-monitor/alerts/alerts-log.md)。
   - 自動アクションと通知を設定するための[アクション ルール](../azure-monitor/alerts/alerts-action-rules.md)。
   - [IT Service Management Connector を使用して Azure を ITSM ツールに接続する](../azure-monitor/alerts/itsmc-overview.md)。