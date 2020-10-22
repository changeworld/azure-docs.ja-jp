---
title: Azure VMware Solution VM のライフサイクル管理
description: Microsoft Azure ネイティブ ツールを使用して、Azure VMware Solution VM のライフサイクルのあらゆる側面を管理する方法について説明します。
ms.topic: conceptual
ms.date: 09/11/2020
ms.openlocfilehash: 928a632a34dd31272c7c3bf92f6dc6dda97cb6cc
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2020
ms.locfileid: "92216251"
---
# <a name="lifecycle-management-of-azure-vmware-solution-vms"></a>Azure VMware Solution VM のライフサイクル管理

Microsoft Azure ネイティブ ツールを使用すると、Azure 環境の仮想マシン (VM) を監視および管理できます。 さらに、Azure VMware Solution の VM とオンプレミスの VM を監視および管理することもできます。 この概要では、Azure の統合監視アーキテクチャについて説明します。また、Azure のネイティブ ツールを使用して、Azure VMware Solution VM をライフサイクル全体にわたって管理する方法についても説明します。

## <a name="benefits"></a>メリット

- Azure ネイティブ サービスを使用して、ハイブリッド環境 (Azure、Azure VMware Solution、オンプレミス) の VM を管理できます。
- Azure、Azure VMware Solution、およびオンプレミスの VM に対する統合された監視と可視性が得られます。
- Azure Automation の Azure Update Management を使用して、Windows マシンと Linux マシンの両方のオペレーティング システムの更新プログラムを管理できます。 
- Azure Security Center により、次のような高度な脅威保護が提供されます。
    - ファイルの整合性の監視
    - ファイルレスのセキュリティ アラート
    - オペレーティング システムのパッチ評価
    - セキュリティの誤った構成の評価
    - エンドポイント保護の評価 
- Azure ARC を使用して、新しい VM に Microsoft Monitoring Agent (MMA) を簡単にデプロイできます。 
- Azure Monitor の Log Analytics ワークスペースにより、MMA または拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 データとログを 1 か所に収集し、そのデータを別の Azure ネイティブ サービスに提供します。 
- Azure Monitor には次のような利点もあります。 
    - シームレスな監視 
    - インフラストラクチャの可視性の向上 
    - インスタント通知 
    - 自動解決 
    - コスト効率 

## <a name="integrated-azure-monitoring-architecture"></a>Azure の統合監視アーキテクチャ

次の図は、Azure VMware Solution VM の統合監視アーキテクチャを示しています。

![Azure の統合監視アーキテクチャ](media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png)

## <a name="integrating-and-deploying-azure-native-services"></a>Azure ネイティブ サービスの統合とデプロイ

**Azure ARC** によって、Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。 Azure ARC は、Azure VMware Solution 環境に Azure Kubernetes Service (AKS) クラスターをインストールすることによってデプロイできます。 詳細については、「[Azure Arc 対応の Kubernetes クラスターを接続する](../azure-arc/kubernetes/connect-cluster.md)」を参照してください。

Azure VMware Solution VM は、MMA (Log Analytics エージェントまたは OMS Linux エージェントとも呼ばれます) を使用して監視できます。 MMA は、VM が ARC VM ライフサイクル ワークフローによってプロビジョニングされている場合、自動的にインストールできます。 MMA は、vCenter のテンプレートから VM をデプロイするときにインストールすることもできます。この場合も、ARC ワークフローによってプロビジョニングされた VM であることが前提です。 プロビジョニングされたすべての Azure VMware Solution VM では、次に MMA をインストールして、Azure Log Analytics ワークスペースにログを送信できます。 詳細については、「[Log Analytics エージェントの概要](../azure-monitor/platform/log-analytics-agent.md)」を参照してください。

**Log Analytics ワークスペース**を使用すると、MMA または拡張機能を使用したログ収集とパフォーマンス カウンターの収集が可能になります。 Log Analytics ワークスペースを作成するには、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/learn/quick-create-workspace.md)」を参照してください。
- Windows VM を Log Analytics ワークスペースに追加するには、「[Windows コンピューターに Log Analytics エージェントをインストールする](../azure-monitor/platform/agent-windows.md)」を参照してください。
- Linux VM を Log Analytics ワークスペースに追加するには、「[Linux コンピューターに Log Analytics エージェントをインストールする](../azure-monitor/platform/agent-linux.md)」を参照してください。

Azure Automation の **Azure Update Management** は、ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。
- Azure Update Management に Log Analytics を追加するには、まず [Azure Automation アカウントを作成する](../automation/automation-create-standalone-account.md)必要があります。
- Log Analytics ワークスペースを Automation アカウントにリンクするには、「[Log Analytics ワークスペースと Automation アカウント](../azure-monitor/insights/solutions.md#log-analytics-workspace-and-automation-account)」を参照してください。
- VM に対して Azure Update Management を有効にするには、「[Automation アカウントから Update Management を有効にする](../automation/update-management/enable-from-automation-account.md)」を参照してください。
- Azure Update Management に VM を追加したら、[VM に更新プログラムをデプロイして結果を確認する](../automation/update-management/deploy-updates.md)ことができます。 

**Azure Security Center** は、クラウドとオンプレミスのハイブリッド ワークロード全体に対して高度な脅威保護を提供します。 Azure VMware Solution VM の脆弱性を評価し、必要に応じてアラートを発生させます。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。
- Azure Security Center はデプロイを必要としません。 詳細については、[仮想マシンでサポートされている機能](../security-center/security-center-services.md)の一覧に関するページを参照してください。
- Azure VMware Solution VM と Azure 以外の VM を Azure Security Center に追加するには、[Windows コンピューターでの Azure Security Center の利用開始](../security-center/quickstart-onboard-machines.md)と、[Linux コンピューターでの Azure Security Center の利用開始](../security-center/quickstart-onboard-machines.md)に関するページを参照してください。
- VM を追加した後、Azure Security Center はリソースのセキュリティの状態を分析し、潜在的な脆弱性を特定します。 また、[概要] タブへの推奨事項の表示も行います。詳細については、「[Azure Security Center でのセキュリティの推奨事項](../security-center/security-center-recommendations.md)」を参照してください。
- Azure Security Center では、セキュリティ ポリシーを定義できます。 セキュリティ ポリシーの構成の詳細については、「[セキュリティ ポリシーの操作](../security-center/tutorial-security-policy.md)」を参照してください。

**Azure Monitor** は、クラウドおよびオンプレミス環境からのテレメトリを収集、分析して対応する、包括的なソリューションです。 デプロイは必要ありません。
- Azure Monitor を使用すると、さまざまなソースからデータを収集して、監視および分析できます。 詳細については、「[Azure Monitor で使用する監視データのソース](../azure-monitor/platform/data-sources.md)」を参照してください。
- 分析や視覚化、アラート用に、さまざまな種類のデータを収集することもできます。 詳細については、「[Azure Monitor データ プラットフォーム](../azure-monitor/platform/data-platform.md)」を参照してください。
- Log Analytics ワークスペースで Azure Monitor を構成するには、「[Azure Monitor for VMs 用に Log Analytics ワークスペースを構成する](../azure-monitor/insights/vminsights-configure-workspace.md)」を参照してください。
- アラート ルールを作成することにより、高いリソースの使用率や不足しているパッチ、ディスク容量の不足、VM のハートビートなどの、環境内の問題を特定できます。 また、IT Service Management (ITSM) ツールにアラートを送信することで、検出されたイベントに対する自動応答を設定することもできます。 アラート検出の通知は、電子メールでも送信できます。 こうしたルールを作成するには、以下を参照してください。
    - [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../azure-monitor/platform/alerts-metric.md)。
    - [Azure Monitor を使用してログ アラートを作成、表示、管理する](../azure-monitor/platform/alerts-log.md)。
    - 自動アクションと通知を設定するための[アクション ルール](../azure-monitor/platform/alerts-action-rules.md)。
    - [IT Service Management Connector を使用して Azure を ITSM ツールに接続する](../azure-monitor/platform/itsmc-overview.md)。

**Azure サービスとしてのプラットフォーム (PaaS)** は、クラウドでの開発およびデプロイ環境であり、クラウドベースのアプリケーションを提供できるようにするためのリソースを備えています。 たとえば Azure SQL Database を、Azure VMware Solution VM と統合できます。 その後、SQL のアラートを、Azure VMware Solution VM のアラートと関連付けることができます。 たとえば、アプリケーションの SQL Database の部分が Azure PAAS 内にあり、同じアプリケーションの Web アプリケーション層が Azure VMware Solution VM でホストされているとします。 このとき、データベース アラートを Web アプリケーション アラートに関連付けることができます。 Azure、Azure VMware Solution、およびオンプレミスの VM に対する可視性が 1 つに統合されることで、トラブルシューティングが簡略化されます。