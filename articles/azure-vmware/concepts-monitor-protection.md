---
title: 概念 - 監視と保護
description: Azure VMware Solution ワークロードのセキュリティ保護と保護に役立つ Azure ネイティブ サービスについて説明します。
ms.topic: conceptual
ms.date: 06/14/2021
ms.openlocfilehash: d735e705ffbccaa0e5fb38951c09b01a22818a0c
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111758261"
---
# <a name="monitor-and-protect-azure-vmware-solution-workloads"></a>Azure VMware Solution ワークロードを監視して保護する

Microsoft Azure ネイティブ サービスを使用して、Azure VMware Solution 上の仮想マシン (VM) とオンプレミスの VM を監視、管理、保護することができます。 Azure VMware Solution と統合できる Azure ネイティブ サービスには、次のものがあります。

- **Log Analytics ワークスペース** は、ログ データを格納するための特有の環境です。 各ワークスペースには、独自のデータ リポジトリと構成があります。 データ ソースとソリューションは、特定のワークスペースにデータを格納するように構成されます。 新しい VM と既存の VM に対して、Azure Arc 対応サーバー VM 拡張機能のサポートを使用して、Log Analytics エージェントを簡単にデプロイできます。 
- **Azure Security Center** は、インフラストラクチャ セキュリティの統合管理システムです。 これにより、データ センターのセキュリティが強化され、クラウドやオンプレミスのハイブリッド ワークロード全体にわたって高度な脅威保護が提供されます。 Azure VMware Solution VM の脆弱性を評価し、必要に応じてアラートを生成します。 このセキュリティ アラートは、解決のために Azure Monitor に転送できます。 Azure Security Center では、セキュリティ ポリシーを定義できます。 詳細については、「[Azure Security Center 統合を使用して Azure VMware Solution VM を保護する](azure-security-integration.md)」を参照してください。
- **[Azure Monitor](../azure-monitor/vm/vminsights-enable-overview.md)** は、クラウドおよびオンプレミス環境からのテレメトリを収集、分析して対応する、包括的なソリューションです。 デプロイは必要ありません。 Azure Monitor を使用すると、ゲスト オペレーティング システムのパフォーマンスを監視し、Azure VMware Solution またはオンプレミスの VM のアプリケーションの依存関係を検出してマップすることができます。 Azure Monitor の Log Analytics ワークスペースにより、Log Analytics エージェントまたは拡張機能を使用して、ログ収集およびパフォーマンス カウンターの収集が可能になります。 データとログを 1 か所に収集し、そのデータを別の Azure ネイティブ サービスに提供します。
- **Azure Arc** によって、Azure VMware Solution、オンプレミス、またはその他のクラウド プラットフォームを含む、あらゆるインフラストラクチャに Azure の管理が拡張されます。 [Azure Arc 対応サーバー](../azure-arc/servers/overview.md)を使用すると、Azure の "*外部*"、企業ネットワーク上、または他のクラウド プロバイダー上でホストされている Windows または Linux の物理サーバーと仮想マシンを管理できます。 [Azure Arc 対応 Kubernetes](../azure-arc/kubernetes/overview.md) を使用して、Azure VMware Solution 環境でホストされている Kubernetes クラスターをアタッチできます。 
- Azure Automation の **[Azure Update Management](../automation/update-management/overview.md)** は、ハイブリッド環境の Windows マシンと Linux マシンのオペレーティング システムの更新プログラムを管理します。 パッチ適用のコンプライアンス状況を監視し、パッチ適用の逸脱を修復するためにアラートを Azure Monitor に転送します。 Azure Update Management は、保存されたデータを使用して VM 上の更新の状態を評価するために、Log Analytics ワークスペースに接続される必要があります。 
 


## <a name="topology"></a>トポロジ

図は、Azure VMware Solution VM の統合監視アーキテクチャを示しています。

:::image type="content" source="media/lifecycle-management-azure-vmware-solutions-virtual-machines/integrated-azure-monitoring-architecture.png" alt-text="統合された Azure 監視アーキテクチャを示す図。" border="false":::

Log Analytics エージェントを使用すると、Azure、Azure VMware Solution、オンプレミスの VM からのログ データの収集が可能になります。 ログ データは Azure Monitor ログに送信され、Log Analytics ワークスペースに格納されます。 Arc 対応サーバーの [VM 拡張機能のサポート](../azure-arc/servers/manage-vm-extensions.md)を使用して、新しい VM や既存の VM のために Log Analytics エージェントをデプロイできます。 

Log Analytics ワークスペースによってログが収集されたら、Azure Security Center を使用して Log Analytics ワークスペースを構成できます。 Azure Security Center によって、Azure VMware Solution VM の脆弱性の状態が評価されて、重大な脆弱性についてアラートが生成されます。 たとえば、オペレーティング システムのパッチの不足、セキュリティの構成の誤り、[エンドポイントの保護](../security-center/security-center-services.md)について評価します。

Log Analytics ワークスペースで、Azure Sentinel によるアラートの検出、脅威の可視性、ハンティング、脅威対応を構成できます。 前の図では、Azure Security Center コネクタを使用して、Azure Security Center が Azure Sentinel に接続されています。 インシデントを作成し、他の脅威とマップするために、Azure Security Center から Azure Sentinel に環境の脆弱性が転送されます。 スケジュールされたルールのクエリを作成して、望ましくないアクティビティを検出し、それをインシデントに変換することもできます。


## <a name="next-steps"></a>次のステップ

Azure VMware Solution のネットワークと相互接続性の概念について理解したら、次の事項の学習に進むことができます。

- [Azure VMware Solution での Azure ネイティブ サービスの統合](integrate-azure-native-services.md)
- [Azure Security Center を Azure VMware Solution と統合する](azure-security-integration.md)
- [Automation アカウントの認証](../automation/automation-security-overview.md)
- [Azure Monitor ログのデプロイの設計](../azure-monitor/logs/design-logs-deployment.md)と [Azure Monitor](../azure-monitor/overview.md)
- [Azure Security Center の計画](../security-center/security-center-planning-and-operations-guide.md)と [Security Center でサポートされているプラットフォーム](../security-center/security-center-os-coverage.md)


