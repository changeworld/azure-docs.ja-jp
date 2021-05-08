---
title: Azure Machine Learning の Azure セキュリティ ベースライン
description: Azure Machine Learning のセキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: machine-learning
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: f8bde45cfdf9cf1c9d50faee76161461d8b0aa0a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104607830"
---
# <a name="azure-security-baseline-for-azure-machine-learning"></a>Azure Machine Learning の Azure セキュリティ ベースライン

このセキュリティ ベースラインでは、[Azure セキュリティ ベンチマーク バージョン 1.0](../security/benchmarks/overview-v1.md) のガイダンスが Microsoft Azure Machine Learning に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。
内容は、**セキュリティ制御** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Azure Machine Learning に適用できる関連ガイダンスによって定義されています。 Azure Machine Learning に適用できない **制御** は、除外されています。

 
Azure Machine Learning を Azure セキュリティ ベンチマークに完全にマップする方法については、[完全な Azure Machine Learning セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines)を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure Machine Learning は、コンピューティング リソースに関して他の Azure サービスに依存します。 コンピューティング リソース (コンピューティング ターゲット) は、モデルのトレーニングとデプロイに使用されます。 これらのコンピューティング先は、仮想ネットワーク内に作成することができます。 たとえば、Azure Virtual Machine Learning コンピューティング インスタンスを使用してモデルをトレーニングしてから、そのモデルを Azure Kubernetes Service (AKS) にデプロイできます。 Azure Machine Learning トレーニング ジョブや推論ジョブを Azure 仮想ネットワーク内で分離して、機械学習のライフサイクルをセキュリティで保護できます。

Azure Firewall を使用して、Azure Machine Learning ワークスペースとパブリック インターネットへのアクセスを制御できます。

- [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)

- [Azure Firewall の内側で Azure Machine Learning のワークスペースを使用する](how-to-access-azureml-behind-firewall.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2:仮想ネットワーク、サブネット、NIC の構成とトラフィックを監視してログに記録する

**ガイダンス**:Azure Machine Learning は、コンピューティング リソースに関して他の Azure サービスに依存します。 Machine Learning のデプロイとして作成されたネットワークにネットワーク セキュリティ グループを割り当てます。 

ネットワーク セキュリティ グループ フロー ログを有効にし、監査のためにログを Azure Storage アカウントに送信します。 また、フロー ログを Log Analytics ワークスペースに送信してから、Traffic Analytics を使用して Azure クラウド内のトラフィック フローに関する分析情報を提供することもできます。 Traffic Analytics のいくつかの利点として、ネットワーク アクティビティを視覚化したり、ホット スポットやセキュリティの脅威を識別したり、トラフィック フロー パターンを把握したり、ネットワークの誤った構成の正確な場所を特定したりする機能が挙げられます。

- [ネットワーク セキュリティ グループのフローのログを有効にする方法](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Traffic Analytics を有効にして使用する方法](../network-watcher/traffic-analytics.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:Azure Machine Learning でデプロイされた Web サービスとの通信をセキュリティで保護するために、HTTPS を有効にすることができます。 Web サービスは、Azure Kubernetes Services (AKS) または Azure Container Instances (ACI) にデプロイされます。これにより、クライアントが送信したデータがセキュリティで保護されます。 また、AKS でプライベート IP を使用すると、仮想ネットワークの背後にあるクライアントのみが Web サービスにアクセスできるようにスコアリングを制限することもできます。

- [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)

- [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: Machine Learning インスタンスに関連付けられた仮想ネットワーク上で DDoS Protection Standard を有効にして、分散型サービス拒否 (DDoS) 攻撃から保護します。 Azure Security Center の統合された脅威検出を使用して、既知の悪意のある通信、または未使用のインターネット IP アドレスとの通信を検出します。

有効化され、悪意のあるネットワーク トラフィックに対して "アラートおよび拒否" するように構成された脅威インテリジェンスベースのフィルター処理とともに、組織の各ネットワーク境界に Azure Firewall をデプロイします。

- [DDoS 保護を構成する方法](../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall の内側で Azure Machine Learning のワークスペースを使用する](how-to-access-azureml-behind-firewall.md)

- [Azure Security Center 脅威インテリジェンスの詳細](../security-center/azure-defender.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Machine Learning サービスに適切な拡張機能が VM にインストールされている場合は、Network Watcher パケット キャプチャを有効にすると、異常なアクティビティを調査できます。 

- [Azure Network Watcher のインスタンスを作成する方法](../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**:悪意のあるトラフィックを検出およびブロックできるように、組織の各ネットワーク境界に任意のファイアウォール ソリューションをデプロイします。

ペイロード検査能力を備えた IDS または IPS 機能をサポートする Azure Marketplace からのプランを選択します。  ペイロード検査が要件でない場合は、Azure Firewall の脅威インテリジェンスを使用できます。 Azure Firewall の脅威インテリジェンス ベースのフィルター処理は、既知の悪意のある IP アドレスおよびドメインとの間のトラフィックに対してアラートを送信したりブロックしたりするために使用されます。 この IP アドレスとドメインのソースは、Microsoft の脅威インテリジェンス フィードです。

- [Azure Firewall をデプロイする方法l](../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../firewall/threat-intel.md)

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**責任**: 適用外

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**: Azure Machine Learning アカウントにアクセスする必要があるリソースについては、Virtual Network サービス タグを使用して、ネットワーク セキュリティ グループまたは Azure Firewall に対するネットワーク アクセス制御を定義します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソース フィールドまたはターゲット フィールドにサービス タグ名 (たとえば AzureMachineLearning) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

Azure Machine Learning service では、複雑さが最小限になるように、仮想ネットワーク内のコンピューティング先のサービス タグの一覧が文書化されます。この一覧は、ネットワーク管理のガイドラインとして使用できます。

- [サービス タグの使用に関する詳細](../virtual-network/service-tags-overview.md)

- [仮想ネットワークの分離とプライバシーの概要](how-to-network-security-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Machine Learning 名前空間に関連付けられているネットワーク リソースの標準的なセキュリティ構成を定義して実装します。 "Microsoft.MachineLearning" と "Microsoft.Network" の名前空間で Azure Policy エイリアスを使用して、Machine Learning 名前空間のネットワーク構成を監査または適用するためのカスタム ポリシーを作成します。 

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**: Azure Machine Learning デプロイに関連付けられているネットワーク リソースを分類に従って論理的に整理するために、それらのリソースにタグを使用します。

[説明] フィールドをサポートする Azure Machine Learning 仮想ネットワーク内のリソースでは、ネットワーク間のトラフィックを許可するルールを文書化するために使用します。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**: Azure アクティビティ ログを使用して、ネットワーク リソース構成を監視し、Azure Machine Learning に関連したネットワーク リソースの変更を検出します。 重要なネットワーク リソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Monitor 経由でログを取り込み、Azure Machine Learning によって生成されたセキュリティ データを集計します。 Azure Monitor で Log Analytics ワークスペースを使用してクエリを発行し、分析を実行して、長期のアーカイブ ストレージには Azure Storage アカウントを使用します。 または、Azure Sentinel またはサードパーティのセキュリティ インシデントおよびイベント管理 (SIEM) に対してデータを有効にしてオンボードすることもできます。

- [Azure Machine Learning の診断ログを構成する方法](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#configuration)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: 監査ログ、セキュリティ ログ、および診断ログにアクセスするために、Azure リソースに対する診断設定を有効にします。 自動的に使用できるアクティビティ ログには、イベント ソース、日付、ユーザー、タイムスタンプ、送信元アドレス、送信先アドレス、その他の役立つ要素が含まれています。

また、セキュリティとコンプライアンスの目的で、Azure Machine Learning service 操作のログを関連付けることもできます。

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](/azure/azure-monitor/platform/diagnostic-settings)

- [Azure でのログ記録とログのさまざまな種類について](/azure/azure-monitor/platform/platform-logs-overview)

- [Azure Machine Learning でログ記録を有効にする](how-to-track-experiments.md)

- [Azure Machine Learning の監視](monitor-azure-machine-learning.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**: コンピューティング リソースが Microsoft に所有されている場合は、Microsoft がその収集と監視を担当します。 

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center を使用してオペレーティング システムを監視します。 

- [Azure Monitor で Azure 仮想マシンの内部ホスト ログを収集する方法](/azure/azure-monitor/learn/quick-collect-azurevm)

- [Azure Security Center のデータ収集について](../security-center/security-center-enable-data-collection.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**:Azure Monitor で、組織のコンプライアンス規則に従って、Azure Machine Learning インスタンスに関連付けられている Log Analytics ワークスペースのログ保有期間を設定します。

- [ログ保持期間のパラメーターを設定する方法](/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**:Azure Machine Learning のリソースから異常な動作がないかログの分析と監視を行い、定期的に結果を確認します。 Azure Monitor と Log Analytics ワークスペースを使用してログを確認し、ログ データに対してクエリを実行します。

または、Azure Sentinel またはサード パーティの SIEM に対してデータを有効にしてオンボードすることもできます。 

- [Log Analytics ワークスペースで Azure Machine Learning に対するクエリを実行する方法](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#analyzing-log-data)

- [Azure Machine Learning でログ記録を有効にする](how-to-track-experiments.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

- [Log Analytics クエリの使用方法](../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](/azure/azure-monitor/log-query/get-started-queries)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Monitor で、アクティビティ ログおよび Machine Learning の診断設定内の Azure Machine Learning に関連するログを構成し、クエリ対象の Log Analytics ワークスペースまたは長期アーカイブ ストレージのストレージ アカウントにログを送信します。 Log Analytics ワークスペースを使用して、セキュリティ ログおよびイベントで異常なアクティビティが検出された場合のアラートを作成します。

または、Azure Sentinel に対してデータを有効にしてオンボードすることもできます。

- [Azure Machine Learning アラートの詳細](https://docs.microsoft.com/azure/machine-learning/monitor-azure-machine-learning#alerts)

- [Log Analytics ワークスペースのログ データに関するアラートを送信する方法](/azure/azure-monitor/learn/tutorial-response)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service のマルウェア対策のデプロイを担当します。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Cloud Services および Virtual Machines 向け Microsoft Antimalware のマルウェア対策イベントの収集を有効にします。

- [クラウド サービスに対して Microsoft Antimalware 拡張機能を構成する方法](/powershell/module/servicemanagement/azure.service/set-azurevmmicrosoftantimalwareextension)

- [Microsoft Antimalware について](../security/fundamentals/antimalware.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 適用できません。Azure Machine Learning では、DNS 関連のログの処理や生成を行いません。

**責任**: 適用外

**Azure Security Center の監視**: なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center を使用して、Azure 仮想マシンのセキュリティ イベント ログ監視を有効にします。 Azure Security Center では、自動プロビジョニングが有効になっている場合に、サポートされているすべての Azure VM と作成された新規のものに対して Log Analytics エージェントがプロビジョニングされます。 または、このエージェントを手動でインストールすることができます。 エージェントにより、プロセス作成イベント 4688 と、イベント 4688 内の commandline フィールドが有効になります。 VM に作成された新しいプロセスは、イベント ログに記録され、Security Center の検出サービスによって監視されます。

- [Azure Security Center でのデータ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure portal でリソースの [ID とアクセスの管理] タブを使用して、Azure Machine Learning リソースで Azure ロールベースのアクセス制御 (Azure RBAC) を構成し、インベントリを維持することができます。 ロールは、Active Directory 内のユーザー、グループ、サービス プリンシパル、およびマネージド ID に適用されます。 組み込みのロールまたはカスタム ロールは、個人とグループに使用できます。

Azure Machine Learning には、Azure Machine Learning の一般的な管理シナリオ向けの組み込みロールが用意されています。 Azure Active Directory (Azure AD) にプロファイルを持つ個人は、これらのロールをユーザー、グループ、サービス プリンシパル、またはマネージド ID に割り当てて、Azure Machine Learning リソース上のリソースと操作へのアクセスを許可または拒否できます。

Azure AD PowerShell モジュールを使用してアドホック クエリを実行し、管理グループのメンバーであるアカウントを検出することもできます。

- [Azure Machine Learning の Azure ロールベースのアクセス制御の概要](how-to-assign-roles.md)

- [PowerShell を使用して Azure AD でディレクトリ ロールを取得する方法](/powershell/module/azuread/get-azureaddirectoryrole)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**:Machine Learning リソースへのアクセス管理は、Azure Active Directory (Azure AD) で制御されます。 Azure AD には既定のパスワードという概念がありません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:Azure Machine Learning では、新しいワークスペースの作成時に 3 つの既定のロールが提供され、所有者アカウントの使用に関する標準的な操作手順が作成されます。

また、Azure Active Directory (Azure AD) Privileged Identity Management および Azure Resource Manager を使用して、管理者アカウントへの Just-In-Time アクセスを有効にすることもできます。

- [既定の Machine Learning ロールの詳細](https://docs.microsoft.com/azure/machine-learning/how-to-assign-roles#default-roles)

- [Privileged Identity Management について](/azure/active-directory/privileged-identity-management/index)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: シングル サインオン (SSO) と Azure Active Directory を統合する

**ガイダンス**: Machine Learning は、Azure Active Directory (Azure AD) に統合されています。サービスごとに個別のスタンドアロン資格情報を構成するのではなく、Azure AD SSO を使用します。 Azure Security Center ID とアクセスの推奨事項を使用してください。

- [Azure AD を使用した SSO の概要](../active-directory/manage-apps/what-is-single-sign-on.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) 多要素認証を有効にし、Azure Security Center の ID およびアクセスの推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: すべての管理タスクに専用マシン (特権アクセス ワークステーション) を使用する

**ガイダンス**:高度な特権を必要とする管理タスクには、セキュリティで保護された Azure マネージド ワークステーション (特権アクセス ワークステーション (PAW) とも呼ばれます) を使用します。

- [セキュリティで保護された Azure マネージド ワークステーションを理解する](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure Active Directory (Azure AD) 多要素認証を有効にする方法](../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**:Azure Active Directory (Azure AD) のセキュリティ レポートと監視を使用して、環境内で疑わしいアクティビティや安全でないアクティビティが発生したときに検出します。 Azure Security Center を使用して ID およびアクセス アクティビティを監視します。

- [危険なアクティビティのフラグが設定された Azure AD ユーザーを識別する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Azure Security Center でユーザーの ID およびアクセス アクティビティを監視する方法](../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-only-from-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: Azure Active Directory (Azure AD) のネームド ロケーションを使用して、IP アドレス範囲または国、地域の特定の論理グループからのアクセスのみを許可します。

- [Azure AD のネームド ロケーションを構成する方法](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。
 
ロール アクセス権は、Azure の複数のレベルにスコープ指定できます。 Machine Learning では、ワークスペース レベルでロールを管理できます。たとえば、ワークスペースへの所有者アクセス権を持つユーザーであっても、そのワークスペースが含まれるリソース グループへの所有者アクセス権を持っていないことがあります。 これにより、同じリソース グループ内の個別のロールへのアクセスをより詳細に制御できます。 

- [Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md) 
 
- [Azure AD インスタンスを作成して構成する方法](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure AD の ID およびアクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザー アクセスを定期的にレビューし、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。

環境内で疑わしいアクティビティや安全でないアクティビティが発生した場合にログやアラートを生成するには、Azure AD Privileged Identity Management (PIM) を使用します。

- [Azure AD のレポートの概要](/azure/active-directory/reports-monitoring)

- [Azure AD の ID およびアクセス レビューの使用方法](../active-directory/governance/access-reviews-overview.md)

- [Azure AD Privileged Identity Management (PIM) をデプロイする](../active-directory/privileged-identity-management/pim-deployment-plan.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: Azure Active Directory (Azure AD) サインイン アクティビティ、監査、およびリスク イベント ログのソースにアクセスできるため、任意の SIEM または監視ツールと統合することができます。

このプロセスを効率化するには、Azure AD ユーザー アカウントの診断設定を作成し、監査ログとサインイン ログを Log Analytics ワークスペースに送信します。 Log Analytics ワークスペース内で必要なアラートを構成できます。

- [Azure アクティビティ ログを Azure Monitor と統合する方法](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-login-behavior-deviation"></a>3.12: アカウント ログイン動作の偏差に関するアラートを生成する

**ガイダンス**: Azure Active Directory (Azure AD) Identity Protection 機能を使用して、ユーザー ID に関連して検出された疑わしいアクションに対する自動応答を構成します。 Azure Sentinel にデータを取り込んで、さらに詳しく調査することもできます。

- [Azure AD の危険なサインインを表示する方法](../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**: 適用できません。Azure Machine Learning service では、カスタマー ロックボックスはサポートされていません。

**責任**: 適用外

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure リソースを追跡しやすくするには、タグを使用します。
 
- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**: 環境の種類やデータの機密度レベルなど、個々のセキュリティ ドメイン用の個別のサブスクリプションと管理グループを使用して分離を実装します。 アプリケーションやエンタープライズ環境で必要とされる Azure リソースへのアクセス レベルを制限できます。 Azure RBAC を使用して Azure リソースへのアクセスを制御できます。
 
- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)

 
- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: Azure Marketplace からのサードパーティ ソリューションをネットワーク境界で使用して、機密情報の承認されていない転送を監視して、情報セキュリティ担当者にアラートを送信すると同時に、そのような転送をブロックします。 

Microsoft によって管理される基になるプラットフォームの場合、Microsoft では顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護します。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。 

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Azure Machine Learning でデプロイされた Web サービスでは、転送中に強制的にデータが暗号化される TLS バージョン 1.2 のみがサポートされています。

- [TLS を使用して Azure Machine Learning による Web サービスをセキュリティで保護する](how-to-secure-web-service.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**: Azure Machine Learning では、データの識別、分類、損失防止機能はまだ使用できません。 コンプライアンスのために必要な場合は、サードパーティ ソリューションを実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客のすべてのコンテンツを機密として扱い、顧客データを損失や漏洩から保護するためにあらゆる手段を尽くします。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-manage-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する

**ガイダンス**: Azure Machine Learning では、Azure Active Directory (Azure AD) を使用して Machine Learning リソースへの要求を承認することがサポートされています。 Azure AD では、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、セキュリティ プリンシパル (ユーザーまたはアプリケーションのサービス プリンシパルである可能性があります) にアクセス許可を付与できます。

- [Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)

- [Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Azure Machine Learning では、Azure Machine Learning ワークスペースとサブスクリプションに関連付けられている Azure BLOB ストレージ アカウントにスナップショット、出力、ログを格納します。 Azure BLOB ストレージに格納されるすべてのデータは、Microsoft によって管理されたキーを使用して保存時に暗号化されます。 また、Machine Learning サービスで独自のキーを使用して、Azure Blob ストレージに格納されているデータを暗号化することもできます。 

- [保存時の Azure Machine Learning データ暗号化](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#encryption-at-rest)

- [Azure での保存時の暗号化の概要](../security/fundamentals/encryption-atrest.md)

- [カスタマー マネージド暗号化キーを構成する方法](../storage/common/customer-managed-keys-configure-key-vault.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**: Azure Machine Learning の運用インスタンスやその他の重要なリソースまたは関連リソースへの変更がいつ発生したかに関するアラートを作成するには、Azure Monitor と Azure アクティビティ ログを使用します。

- [Azure アクティビティ ログ イベントのアラートを作成する方法](/azure/azure-monitor/platform/alerts-activity-log)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service の脆弱性管理を担当します。 

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure 仮想マシン、コンテナー イメージ、および SQL サーバーに対して脆弱性評価を実行することに関する Azure Security Center の推奨事項に従います。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**: Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service の修正プログラム管理を担当します。 

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Automation Update Management を使用して、最新のセキュリティ更新プログラムが Windows および Linux VM にインストールされていることを確認します。 Windows VM については、Windows Update が有効になっていて、自動的に更新するよう設定されていることを確認します。

- [Azure で仮想マシンに対して Update Management を構成する方法](../automation/update-management/overview.md)

- [Security Center によって監視される Azure セキュリティ ポリシーについて理解する](../security-center/policy-reference.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、サードパーティの修正プログラム管理ソリューションを使用します。 環境内で既に Configuration Manager を使用しているお客様は、System Center Updates Publisher を使用して、カスタム更新プログラムを Windows Server Update Service に発行することもできます。 これにより、Update Management では、サード パーティ ソフトウェアを使用して、Configuration Manager を更新リポジトリとして使用するマシンに修正プログラムを適用できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure 仮想マシン、コンテナー イメージ、および SQL サーバーに対して脆弱性評価を実行することに関する Azure Security Center の推奨事項に従います。 スキャン結果を一定の間隔でエクスポートして、前回のスキャンと結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合は、選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: 適用できません。このガイドラインは、コンピューティング リソースを対象にしています。

**責任**: 適用外

**Azure Security Center の監視**: なし

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**: Azure Resource Graph を使用して、サブスクリプション内のリソース (コンピューティング、ストレージ、ネットワーク、ポート、プロトコルなど) のクエリおよび検出を行います。 テナントで適切な (読み取り) アクセス許可を確認し、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙します。

従来の Azure リソースは Azure Resource Graph Explorer で検出できますが、今後は Azure Resource Manager リソースを作成して使用することを強くお勧めします。

- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**:メタデータを追加して Azure リソースにタグを適用すると、それらのリソースが各分類に応じて論理的に整理されます。

- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**: 必要に応じて、タグ付け、管理グループ、および個別のサブスクリプションを使用して、資産の整理と追跡を行います。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../governance/management-groups/create-management-group-portal.md)
 
- [タグを作成して使用する方法](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 組織のニーズに応じて、承認された Azure リソースとコンピューティング リソース用に承認されたソフトウェアのインベントリを作成します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

* 許可されないリソースの種類
* 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Automation Change Tracking と Inventory を使用して、Windows および Linux VM からのインベントリ情報の収集を自動化します。 ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 ソフトウェアのインストール日やその他の情報を入手するには、ゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込みます。

- [VM の Azure Automation インベントリ収集を有効にする方法](../automation/automation-tutorial-installed-software.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center のファイルの整合性の監視 (FIM) を使用して、VM にインストールされているすべてのソフトウェアを特定します。 FIM の代わりに、または FIM と組み合わせて使用できるもう 1 つのオプションとして、Azure Automation Change Tracking と Inventory を使用して、Linux および Windows VM からインベントリを収集することもできます。 

承認されていないソフトウェアを削除するための独自のプロセスを実装することができます。 サードパーティ製のソリューションを使用して、承認されていないソフトウェアを特定することもできます。

不要になったら Azure リソースを削除します。

- [ファイルの整合性の監視を使用する方法](../security-center/security-center-file-integrity-monitoring.md)

- [Azure Automation Change Tracking と Inventory の概要](../automation/change-tracking/overview.md)

- [Azure 仮想マシンのインベントリを有効にする方法](../automation/automation-tutorial-installed-software.md)

- [Azure リソース グループとリソースの削除](../azure-resource-manager/management/delete-resource-group.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみが実行され、承認されていないすべてのソフトウェアの実行がブロックされるようにできます。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**: 次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。

* 許可されないリソースの種類
* 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプションのリソースのクエリまたは検出を行えます。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Graph を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**:Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center の適応型アプリケーション制御を使用して、規則が適用されるかどうかを指定します。

適応型アプリケーション制御が要件を満たしていない場合は、サードパーティ ソリューションを実装します。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: 適用外

**Azure Security Center の監視**: なし

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**: "Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure Active Directory (Azure AD) 条件付きアクセスを使用します。

- [条件付きアクセスを構成して Azure Resource Manager へのアクセスをブロックする方法](../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12:コンピューティング リソースでスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、スクリプトの種類に基づき、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーの Azure コンピューティング リソースでスクリプトを実行する機能を制限できます。 また、Azure Security Center の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../security-center/security-center-adaptive-application.md)

**責任**: 適用外

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: 適用できません。この推奨事項は、Azure App Service またはコンピューティング リソース上で実行されている Web アプリケーションを対象にしています。

**責任**: 適用外

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy を使用して、Azure Machine Learning service の標準的なセキュリティ構成を定義して実装します。 Azure Machine Learning サービスの構成を監査または適用するためのカスタム ポリシーを作成するには、"Microsoft.MachineLearning" 名前空間で Azure Policy エイリアスを使用します。

Azure Resource Manager には、JavaScript Object Notation (JSON) でテンプレートをエクスポートする機能があり、構成が確実に組織のセキュリティ要件を満たすように確認する必要があります。

また、ご利用の Azure リソース用の安全な構成基準として Azure Security Center からのレコメンデーションを使用することもできます。

Azure Machine Learning では、作業を追跡するために Git リポジトリが完全にサポートされています。したがって、自分の共有ワークスペース ファイル システムにリポジトリをクローンしたり、ローカル ワークステーションで Git を使用したり、CI/CD パイプラインから Git を使用したり、Machine Learning 環境の一部としてセキュリティで保護された構成がコード リソースに適用されるようにしたりできます。

- [使用可能な Azure Policy エイリアスを表示する方法](/powershell/module/az.resources/get-azpolicyalias)

- [チュートリアル:コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)

- [Azure portal のテンプレートへの単一および複数リソースのエクスポート](../azure-resource-manager/templates/export-template-portal.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**:Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service のオペレーティング システムのセキュリティで保護された構成を担当します。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center の推奨事項を使用して、すべてのコンピューティング リソースのセキュリティ構成を維持します。  さらに、カスタム オペレーティング システム イメージまたは Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を確立できます。

- [Azure Security Center の推奨事項を監視する方法](../security-center/security-center-recommendations.md)

- [セキュリティの推奨事項 - リファレンス ガイド](../security-center/recommendations-reference.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

- [VHD をアップロードし、それを使用して Azure で新しい Windows VM を作成する](../virtual-machines/windows/upload-generalized-managed.md)

- [Azure CLI を使用してカスタム ディスクから Linux VM を作成する](../virtual-machines/linux/upload-vhd.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**:Azure リソース全体にセキュリティで保護された設定を適用するには、Azure Policy の [deny] と [deploy if not exist] を使用します。 さらに、Azure Resource Manager テンプレートを使用して、組織に必要な Azure リソースのセキュリティ構成を維持できます。 
 
- [Azure Policy の効果について](../governance/policy/concepts/effects.md)
 
- [コンプライアンスを強制するポリシーの作成と管理](../governance/policy/tutorials/create-and-manage.md)
 
- [Azure Resource Manager テンプレートの概要](../azure-resource-manager/templates/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**:Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service のオペレーティング システムのセキュリティで保護された構成を担当します。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure コンピューティング リソースに対して脆弱性評価を実行することに関する Azure Security Center の推奨事項に従います。  さらに、Azure Resource Manager テンプレート、カスタム オペレーティング システム イメージ、または Azure Automation State Configuration を使用して、組織に必要なオペレーティング システムのセキュリティ構成を維持できます。   Microsoft 仮想マシン テンプレートと Azure Automation State Configuration を組み合わせると、セキュリティ要件を満たし、それを維持するために役立ちます。 

Microsoft によって公開された Azure Marketplace の仮想マシン イメージが Microsoft によって管理および維持されることに注意してください。 

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../security-center/deploy-vulnerability-assessment-vm.md)

- [ARM テンプレートから Azure 仮想マシンを作成する方法](../virtual-machines/windows/ps-template.md)

- [Azure Automation State Configuration の概要](../automation/automation-dsc-overview.md)

- [Azure portal で Windows 仮想マシンを作成する](../virtual-machines/windows/quick-create-portal.md)

- [VM テンプレートをダウンロードする方法に関する情報](/previous-versions/azure/virtual-machines/windows/download-template)

- [VHD を Azure にアップロードし、新しい VM を作成するサンプル スクリプト](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: Machine Learning または関連リソースにカスタムの Azure Policy 定義を使用している場合は、Azure Repos を使用してコードを安全に格納して管理します。

Azure Machine Learning では、作業を追跡するために Git リポジトリが完全にサポートされています。したがって、自分の共有ワークスペース ファイル システムにリポジトリをクローンしたり、ローカル ワークステーションで Git を使用したり、CI/CD パイプラインから Git を使用したり、Machine Learning 環境の一部としてセキュリティで保護された構成がコード リソースに適用されるようにしたりできます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure Repos のドキュメント](/azure/devops/repos/)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織が所有するコンピューティング リソースの場合は、Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、許可されているユーザーのみがカスタム イメージにアクセスできるようにします。 Azure Shared Image Gallery を使用すると、組織内のさまざまなユーザー、サービス プリンシパル、Azure Active Directory (Azure AD) グループに対してイメージを共有できます。 コンテナー イメージを Azure Container Registry に格納し、Azure RBAC を使用して、許可されているユーザーのみがアクセスできるようにします。

- [Azure RBAC について](../role-based-access-control/rbac-and-directory-admin-roles.md)

- [コンテナー レジストリの Azure RBAC を理解する](../container-registry/container-registry-roles.md)

- [Azure RBAC を構成する方法](../role-based-access-control/quickstart-assign-role-user-portal.md)

- [共有イメージ ギャラリーの概要](../virtual-machines/shared-image-galleries.md)

- [Kubernetes 認可に Azure RBAC を使用する](../aks/manage-azure-rbac.md)

**責任**: 適用外

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**: システム構成を警告処理、監査、適用するためのカスタム ポリシーを作成するには、"Microsoft.MachineLearning" 名前空間で Azure Policy エイリアスを使用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../governance/policy/tutorials/create-and-manage.md)

- [エイリアスを使用する方法](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**: Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service のセキュリティ保護された構成のデプロイを担当します。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、クラウドまたはオンプレミスのデータセンターで Desired State Configuration (DSC) ノード用の Azure Automation State Configuration を使用します。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。 

- [Azure Automation State Configuration を有効にする方法](../automation/automation-dsc-onboarding.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure Security Center を使用して、ご利用の Azure リソースのベースライン スキャンを実行します。 さらに、Azure Policy を使用して Azure リソース構成をアラート送信および監査します。
 
 
 
- [Azure Security Center の推奨事項を修復する方法](../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Microsoft がコンピューティング リソースを所有している場合は、Microsoft が Azure Machine Learning service のセキュリティで保護された構成の自動監視を担当します。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center Compute &amp; アプリを使用し、VM、サーバー、およびコンテナーの推奨事項に従います。

- [Azure Security Center のコンテナーの推奨事項を理解する](../security-center/container-security.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド ID を Azure Key Vault と組み合わせて使用して、クラウド アプリケーションのシークレット管理を簡素化します。

Azure Machine Learning では、カスタマー マネージド キーを使用したデータストアの暗号化がサポートされています。組織のセキュリティとコンプライアンスの要件に従って、キーのローテーションと取り消しを管理する必要があります。 

トレーニング スクリプトでクリアテキストの代わりに Azure Key Vault を使用して、安全にシークレットをリモート実行に渡します。

- [Azure Machine Learning のカスタマー マネージド キー](https://docs.microsoft.com/azure/machine-learning/concept-enterprise-security#azure-blob-storage)

- [Azure Machine Learning トレーニングの実行で認証資格情報シークレットを使用する](how-to-use-secrets-in-runs.md)

- [Azure リソースに対してマネージド ID を使用する方法](../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](../key-vault/general/quick-create-portal.md)

- [Key Vault に対して認証を行う方法](../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: Azure Machine Learning では、組み込みロールと、カスタム ロールを作成する機能の両方がサポートされています。 マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [Azure Machine Learning ワークスペースへのアクセスの管理](how-to-assign-roles.md)

- [VM で Azure リソース用のマネージド ID を構成する方法](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。 

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1:一元管理されるマルウェア対策ソフトウェアを使用する

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Machine Learning など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure 用の Microsoft Antimalware を使用して、リソースを継続的に監視して保護します。 Linux の場合は、サードパーティのマルウェア対策ソリューションを使用します。 さらに、データ サービス向けの Azure Security Center の脅威検出を使用して、ストレージ アカウントにアップロードされたマルウェアを検出します。

- [Azure に対して Microsoft Antimalware を構成する方法](../security/fundamentals/antimalware.md)

- [Azure Security Center での脅威の防止](../security-center/azure-defender.md)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="82-pre-scan-files-to-be-uploaded-to-non-compute-azure-resources"></a>8.2:非コンピューティング Azure リソースにアップロードするファイルを事前にスキャンする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Machine Learning など) をサポートしている基になるホストで有効になっていますが、顧客のコンテンツに対しては実行されません。

非コンピューティング Azure リソースにアップロードされている任意のコンテンツを事前にスキャンするのは、お客様の責任となります。 Microsoft は、お客様のデータにアクセスできないため、お客様に代わってお客様のコンテンツのマルウェア対策スキャンを実行することはできません。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="83-ensure-antimalware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Microsoft のマルウェア対策は、Azure サービス (Azure Machine Learning など) をサポートしている基になるホストで有効になっていて、管理されていますが、顧客のコンテンツに対しては実行されません。

Azure Machine Learning では、さまざまなコンピューティング リソース (ユーザー独自のコンピューティング リソース含む) にわたって、さまざまなサポートが提供されています。 ユーザーの組織がコンピューティング リソースを所有している場合は、Azure Security Center の [計算とアプリ] の推奨事項に従って、すべてのエンドポイントが最新の署名付きの最新状態になっていることを確認します。&amp; Linux の場合は、サードパーティのマルウェア対策ソリューションを使用します。

- [Azure 向け Microsoft Antimalware をデプロイする方法](../security/fundamentals/antimalware.md)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的に自動バックアップを行う

**ガイダンス**: Azure Machine Learning service では、接続されたデータ ストアのデータ管理によってデータの復旧が管理されます。 必ず、接続されたストア向けのデータ復旧ガイドラインに従って、顧客組織のポリシーに従ってデータをバックアップしてください。

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: Azure Machine Learning service では、接続されたデータ ストアのデータ管理によってデータがバックアップされます。 VM の Azure Backup を有効にし、必要な頻度と保持期間を構成します。 Azure Key Vault でカスタマー マネージド キーをバックアップする

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Machine Learning service では、接続されたデータ ストアのデータ管理によってデータのバックアップが検証されます。 Azure Backup でコンテンツのデータ復元を定期的に実行します。 バックアップされたカスタマー マネージド キーを復元できることを確認します。

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../backup/backup-azure-restore-files-from-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: オンプレミスのバックアップでは、Azure にバックアップする際に指定するパスフレーズを使用して保存時の暗号化が行われます。 Azure ロールベースのアクセス制御 (Azure RBAC) を使用して、バックアップとカスタマー マネージド キーを保護します。 

Key Vault で論理的な削除と消去保護を有効にして、偶発的または悪意のある削除からキーを保護します。 Azure Storage を使用してバックアップを格納した場合、BLOB または BLOB のスナップショットが削除されたときに、論理的な削除機能でデータを保存および復旧することができます。

- [Azure RBAC について](../role-based-access-control/overview.md)

- [Key Vault で論理的な削除と消去保護を有効にする方法](../storage/blobs/soft-delete-blob-overview.md)

- [Azure Blob Storage の論理的な削除](../storage/blobs/soft-delete-blob-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールと、検出からインシデント後のレビューまでのインシデント対応と管理のフェーズを定義する、書面によるインシデント対応計画があることを確認します。 

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [独自のインシデント対応計画を作成するために NIST の「コンピューター セキュリティ インシデント対応ガイド」を使用する](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Azure Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

さらに、タグを使用してサブスクリプションをマークし、Azure リソース (特に、機密データを処理するもの) を識別して分類するための命名システムを作成します。 インシデントが発生した Azure リソースと環境の重要度に基づいてアラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**: ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて対応計画を見直します。

- [NIST の出版物 -- IT 計画と機能に関するテスト、トレーニング、および演習プログラムのガイド](https://csrc.nist.gov/publications/detail/sp/800-84/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して自動的に応答をトリガーし、Azure リソースを保護できます。

- [Security Center でワークフロー自動化を構成する方法](../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft クラウド侵入テストの実施ルールに従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
