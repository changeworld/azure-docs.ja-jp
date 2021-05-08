---
title: Windows Virtual Machines 用の Azure のセキュリティ ベースライン
description: Windows Virtual Machines セキュリティ ベースラインでは、Azure セキュリティ ベンチマークで指定されているセキュリティに関する推奨事項を実装するための手順のガイダンスとリソースが提供されます。
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 4478ab6e1ec53f00350d692ed0d7ad838dec02f2
ms.sourcegitcommit: c6a2d9a44a5a2c13abddab932d16c295a7207d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107284921"
---
# <a name="azure-security-baseline-for-windows-virtual-machines"></a>Windows Virtual Machines 用の Azure のセキュリティ ベースライン

このセキュリティ ベースラインにより、[Azure セキュリティ ベンチマーク バージョン 1.0](../../security/benchmarks/overview-v1.md) のガイダンスが Windows Virtual Machines に適用されます。 Azure セキュリティ ベンチマークには、Azure 上のクラウド ソリューションをセキュリティで保護する方法に関する推奨事項がまとめてあります。 内容は、**セキュリティ コントロール** によってグループ化されています。これは、Azure セキュリティ ベンチマークと、Windows Virtual Machines に適用できる関連ガイダンスによって定義されています。

> [!NOTE]
> Windows Virtual Machines に適用されない **コントロール**、または Microsoft が責任を持つものは、除外されています。 Windows Virtual Machines を Azure セキュリティ ベンチマークに完全にマップする方法については、 **[完全な Windows Virtual Machines セキュリティ ベースライン マッピング ファイル](https://github.com/MicrosoftDocs/SecurityBenchmarks/blob/master/Azure%20Offer%20Security%20Baselines/1.1/windows-virtual-machines-security-baseline-v1.1.xlsx)** を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

*詳細については、[Azure セキュリティ ベンチマークの「ネットワークのセキュリティ](../../security/benchmarks/security-control-network-security.md)」を参照してください。*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1:仮想ネットワーク内の Azure リソースを保護する

**ガイダンス**: Azure 仮想マシン (VM) を作成する場合は、仮想ネットワーク (VNet) を作成するか、または既存の VNet を使用し、サブネットを使用して VM を構成する必要があります。 デプロイされたすべてのサブネットに、アプリケーションの信頼されたポートおよびソースに固有のネットワーク アクセス制御が適用されたネットワーク セキュリティ グループがあることを確認します。 

あるいは、一元化されたファイアウォールの特定のユース ケースがある場合は、Azure Firewall を使用してこれらの要件を満たすこともできます。

- [Azure における仮想ネットワークと仮想マシン](/azure/virtual-machines/windows/network-overview)

- [仮想ネットワークを作成する方法](../../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイして構成する方法](../../firewall/tutorial-firewall-deploy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-1-1.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.1](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-1.md)]

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-network-interfaces"></a>1.2:仮想ネットワーク、サブネット、ネットワーク インターフェイスの構成とトラフィックを監視してログに記録する

**ガイダンス**: Azure の Azure 仮想マシン (VM) リソースのセキュリティ保護に役立つようにネットワーク保護に関する推奨事項を識別し、それに従うには、Azure Security Center を使用します。 NSG フロー ログを有効にし、VM の通常とは異なるアクティビティのトラフィック監査のためにログをストレージ アカウントに送信します。

- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Azure Security Center によって提供されるネットワークのセキュリティについて](../../security-center/security-center-network-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="13-protect-critical-web-applications"></a>1.3:重要な Web アプリケーションを保護する

**ガイダンス**:仮想マシン (VM) を使用して Web アプリケーションをホストしている場合は、その VM のサブネットでネットワーク セキュリティ グループ (NSG) を使用して、通信が許可されるネットワーク トラフィック、ポート、プロトコルを制限します。 アプリケーションへの必要なトラフィックのみを許可するように NSG を構成する場合は、最小特権のネットワーク アプローチに従います。

また、着信トラフィックの検査を強化するために、重要な Web アプリケーションの前に Azure Web アプリケーション ファイアウォール (WAF) をデプロイすることもできます。 WAF の診断設定を有効にし、ストレージ アカウント、イベント ハブ、または Log Analytics ワークスペースにログを取り込みます。 

- [Azure portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する](../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)

- [ネットワーク セキュリティ グループに関する情報](../../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4:既知の悪意のある IP アドレスとの通信を拒否する

**ガイダンス**: DDoS 攻撃から保護するには、仮想ネットワークで DDoS (分散型サービス拒否) Standard 保護を有効にします。 Azure Security Center の統合された脅威インテリジェンスを使用すると、既知の悪意のある IP アドレスとの通信を監視できます。  各 Virtual Network セグメントで Azure Firewall を適切に構成して、脅威インテリジェンスを有効にし、悪意のあるネットワーク トラフィックに対して [警告して拒否] を構成します。

Azure Security Center の Just In Time ネットワーク アクセスを使用すると、限られた期間について、承認された IP アドレスへの Windows Virtual Machines の公開を制限できます。  また、実際のトラフィックと脅威インテリジェンスに基づいてポートとソース IP を制限する NSG 構成を推奨する Azure Security Center のアダプティブ ネットワーク強化を使用します。

- [DDoS 保護を構成する方法](../../ddos-protection/manage-ddos-protection.md)

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Security Center の統合された脅威インテリジェンスについて](../../security-center/azure-defender.md)

- [Azure Security Center のアダプティブ ネットワークのセキュリティ強化について](../../security-center/security-center-adaptive-network-hardening.md)

- [Azure Security Center の Just In Time ネットワーク アクセス制御について](../../security-center/security-center-just-in-time.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.4](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-4.md)]

### <a name="15-record-network-packets"></a>1.5:ネットワーク パケットを記録する

**ガイダンス**: Azure Virtual Machines のフロー レコードを生成するために、NSG フロー ログをストレージ アカウントに記録できます。 異常なアクティビティを調査するときに、Network Watcher パケット キャプチャを有効にして、通常とは異なる異常なアクティビティがないかネットワーク トラフィックを確認できるようにすることができます。

- [NSG フロー ログを有効にする方法](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Network Watcher を有効にする方法](../../network-watcher/network-watcher-create.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6:ネットワーク ベースの侵入検出または侵入防止システム (IDS または IPS) をデプロイする

**ガイダンス**: Network Watcher によって提供されるパケット キャプチャとオープン ソースの IDS ツールを組み合わせることにより、広範囲の脅威に対するネットワーク侵入検出を実行できます。 また、必要に応じて Virtual Network セグメントで Azure Firewall をデプロイして、脅威インテリジェンスを有効にし、悪意のあるネットワーク トラフィックに対して [警告して拒否] を構成することもできます。

- [Network Watcher とオープン ソース ツールを使用してネットワーク侵入検出を実行する](../../network-watcher/network-watcher-intrusion-detection-open-source-tools.md)

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートを構成する方法](../../firewall/threat-intel.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="17-manage-traffic-to-web-applications"></a>1.7:Web アプリケーションへのトラフィックを管理する

**ガイダンス**: 信頼された証明書に対して HTTPS/SSL が有効になった Web アプリケーションのために Azure Application Gateway をデプロイできます。 Azure Application Gateway では、ポートにリスナーを割り当て、規則を作成し、Windows Virtual Machines などのバックエンド プールにリソースを追加することによって、アプリケーション Web トラフィックを特定のリソースに転送します。

- [Application Gateway をデプロイする方法](../../application-gateway/quick-create-portal.md)

- [HTTPS を使用するように Application Gateway を構成する方法](../../application-gateway/create-ssl-portal.md)

- [Azure Web アプリケーション ゲートウェイを使用したレイヤー 7 負荷分散について](../../application-gateway/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8:ネットワーク セキュリティ規則の複雑さと管理オーバーヘッドを最小限に抑える

**ガイダンス**:Azure 仮想マシンで構成されているネットワーク セキュリティ グループまたは Azure Firewall でのネットワーク アクセス制御を定義するには、Virtual Network サービス タグを使用します。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切なソースまたは宛先フィールドにサービス タグ名 (ApiManagement など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。

- [サービス タグとその使用方法の概要](../../virtual-network/service-tags-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9:ネットワーク デバイスの標準的なセキュリティ構成を維持する

**ガイダンス**: Azure Policy を使用して、Azure Virtual Machines (VM) の標準的なセキュリティ構成を定義して実装します。 また、Azure Blueprints を使用して、Azure Resource Manager テンプレート、ロールの割り当て、Azure Policy 割り当てなどの主要な環境成果物を単一のブループリント定義にパッケージ化することにより、大規模な Azure VM デプロイを簡略化することもできます。 このブループリントをサブスクリプションに適用し、ブループリントのバージョン管理によるリソース管理を有効にすることができます。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

- [Azure Blueprint を作成する方法](../../governance/blueprints/create-blueprint-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="110-document-traffic-configuration-rules"></a>1.10:トラフィック構成規則を文書化する

**ガイダンス**:Windows 仮想マシン用に構成されている、ネットワーク セキュリティやトラフィック フローに関連するネットワーク セキュリティ グループ (NSG) やその他のリソースのタグを使用できます。 個々の NSG 規則については、[説明] フィールドを使用して、ネットワークとの間のトラフィックを許可するすべての規則のビジネス ニーズや期間を指定します。

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11:自動化ツールを使用してネットワーク リソース構成を監視し、変更を検出する

**ガイダンス**:仮想マシンに関連するネットワーク リソース構成への変更を監視するには、Azure アクティビティ ログを使用します。 重要なネットワーク設定またはリソースへの変更が発生するとトリガーされる Azure Monitor 内のアラートを作成します。

Windows Virtual Machines に関連するネットワーク リソースの構成を検証 (または修復) するには、Azure Policy を使用します。

- [Azure アクティビティ ログ イベントを表示して取得する方法](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Azure Monitor でアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [ネットワークに関する Azure Policy のサンプル](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 1.11](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-1-11.md)]

## <a name="logging-and-monitoring"></a>ログ記録と監視

*詳細については、[Azure セキュリティ ベンチマークの「ログ記録と監視](../../security/benchmarks/security-control-logging-monitoring.md)」を参照してください。*

### <a name="22-configure-central-security-log-management"></a>2.2:セキュリティ ログの一元管理を構成する

**ガイダンス**: Azure Security Center は、Windows Virtual Machines のセキュリティ イベント ログの監視を提供します。 セキュリティ イベント ログによって生成されるデータの量によっては、既定で保存されません。

- [組織で仮想マシンからのセキュリティ イベント ログ データを保持する場合は、データ収集レベル内で格納でき、その後 Log Analytics でそのクエリを実行できます。さまざまなレベルがあります:最小、共通、およびすべて。これらの詳細は、次のリンクに記載されています](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.2](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-2.md)]

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3:Azure リソースの監査ログ記録を有効にする

**ガイダンス**: アクティビティ ログを使用すると、仮想マシン リソースで実行される操作やアクションを監査できます。 アクティビティ ログには、読み取り操作 (GET) を除く、リソースへのすべての書き込み操作 (PUT、POST、DELETE) が含まれています。 アクティビティ ログを使用すると、トラブルシューティング時にエラーを見つけたり、組織内のユーザーがリソースをどのように変更したかを監視したりできます。

Virtual Machines (VM) に診断拡張機能をデプロイすることによって、ゲスト OS 診断データの収集を有効にします。 診断拡張機能を使用すると、Azure 仮想マシンからアプリケーション ログやパフォーマンス カウンターなどの診断データを収集できます。

仮想マシンでサポートされているアプリケーションやサービスの高度な可視性を実現するには、Azure Monitor for VMs と Application Insights の両方を有効にすることができます。 Application Insights を使用すると、アプリケーションを監視して、HTTP 要求や例外などのテレメトリをキャプチャできるため、VM とアプリケーションの間の問題を相互に関連付けることができます。

さらに、イベント ソース、日付、ユーザー、タイムスタンプ、ソース アドレス、宛先アドレス、その他の役立つ要素が含まれている監査およびアクティビティ ログへのアクセスのために Azure Monitor を有効にします。 

- [Azure Monitor でプラットフォーム ログとメトリックを収集する方法](https://docs.microsoft.com/azure/azure-monitor/essentials/activity-log#view-the-activity-log)

- [Log Analytics エージェントの概要](../../azure-monitor/agents/log-analytics-agent.md)

- [Windows 用の Log Analytics 仮想マシン拡張機能](../extensions/oms-windows.md)

- [Azure アクティビティ ログ イベントを表示および取得する](/azure/azure-monitor/platform/activity-log#view-the-activity-log)

- [Application Insights の概要](../../azure-monitor/app/app-insights-overview.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.3](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-3.md)]

### <a name="24-collect-security-logs-from-operating-systems"></a>2.4:オペレーティング システムからセキュリティ ログを収集する

**ガイダンス**:Azure Virtual Machines のセキュリティ イベント ログの監視を提供するには、Azure Security Center を使用します。 セキュリティ イベント ログによって生成されるデータの量によっては、既定で保存されません。 

組織で仮想マシンのセキュリティ イベント ログ データを保持したい場合は、そのデータを Azure Security Center 内に構成された目的のデータ収集レベルの Log Analytics ワークスペース内に格納できます。

- [Azure Security Center でのデータ収集](../../security-center/security-center-enable-data-collection.md)

- [監視のために Syslog データをキャプチャするには、Log Analytics 拡張機能を有効にする必要がある](https://docs.microsoft.com/azure/azure-monitor/vm/quick-collect-azurevm#enable-the-log-analytics-vm-extension)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.4](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-4.md)]

### <a name="25-configure-security-log-storage-retention"></a>2.5:セキュリティ ログのストレージ保持を構成する

**ガイダンス**: 仮想マシン ログを格納するために使用されるすべてのストレージ アカウントまたは Log Analytics ワークスペースに、組織のコンプライアンス規則に従ったログの保有期間が設定されていることを確認します。

- [Azure で仮想マシンを監視する方法](../../azure-monitor/vm/monitor-vm-azure.md)

- [Log Analytics ワークスペースの保有期間を構成する方法](../../azure-monitor/logs/manage-cost-storage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="26-monitor-and-review-logs"></a>2.6:ログを監視して確認する

**ガイダンス**: Log Analytics エージェント (Microsoft Monitoring Agent (MMA) とも呼ばれます) を有効にし、それを Log Analytics ワークスペースにログを送信するように構成します。 Windows エージェントは、さまざまなソースから収集されたデータや、監視ソリューションで定義されているすべての固有のログまたはメトリックを Azure Monitor の Log Analytics ワークスペースに送信します。

異常な動作がないかログの分析と監視を行って、定期的に結果を確認します。 Azure Monitor を使用して、ログを確認したり、ログ データにクエリを実行したりします。

あるいは、データを有効にして Azure Sentinel またはサード パーティの SIEM にオンボードすることにより、ログを監視および確認することもできます。

- [Log Analytics エージェントの概要](../../azure-monitor/agents/log-analytics-agent.md)

- [Windows 用の Log Analytics 仮想マシン拡張機能](../extensions/oms-windows.md)

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

- [Log Analytics ワークスペースについて](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure Monitor でカスタム クエリを実行する方法](../../azure-monitor/logs/get-started-queries.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7:異常なアクティビティについてのアラートを有効にする

**ガイダンス**: Azure Virtual Machines のセキュリティ ログやイベントで検出される異常なアクティビティを監視し、それに関するアラートを生成するには、Log Analytics ワークスペースと共に構成された Azure Security Center を使用します。  

あるいは、データを有効にして Azure Sentinel またはサード パーティの SIEM にオンボードすることにより、異常なアクティビティに関するアラートを設定することもできます。

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

- [Azure Security Center でアラートを管理する方法](../../security-center/security-center-managing-and-responding-alerts.md)

- [Log Analytics のログ データに関するアラートを送信する方法](../../azure-monitor/alerts/tutorial-response.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="28-centralize-anti-malware-logging"></a>2.8:マルウェア対策のログ記録を一元管理する

**ガイダンス**: Azure Cloud Services および Virtual Machines 用 Microsoft Antimalware を使用して、Azure Storage アカウントにイベントを記録するように仮想マシンを構成できます。 Storage アカウントからイベントを取り込み、必要に応じてアラートを作成するように、Log Analytics ワークスペースを構成します。 Azure Security Center の[Compute &amp; Apps]\(計算とアプリ\) の推奨事項に従います。 

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../../security/fundamentals/antimalware.md)

- [Virtual Machines のゲストレベルの監視を有効にする方法](../../cost-management-billing/cloudyn/azure-vm-extended-metrics.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 2.8](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-2-8.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 2.8](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-2-8.md)]

### <a name="29-enable-dns-query-logging"></a>2.9:DNS クエリのログ記録を有効にする

**ガイダンス**: 組織のニーズに応じて、DNS ログ ソリューション用に Azure Marketplace のサードパーティ製ソリューションを実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="210-enable-command-line-audit-logging"></a>2.10:コマンドライン監査ログ記録を有効にする

**ガイダンス**: Azure Security Center により、Azure Virtual Machines (VM) のセキュリティ イベント ログの監視が提供されます。  自動プロビジョニングが有効になっている場合、Security Center によって、サポートされているすべての Azure VM と新しく作成されるものに Microsoft Monitoring Agent がプロビジョニングされます。またはエージェントを手動でインストールできます。 エージェントにより、プロセス作成イベント 4688 とイベント 4688 内の CommandLine フィールドが有効になります。 VM に作成された新しいプロセスは EventLog に記録され、Security Center の検出サービスによって監視されます。

- [Azure Security Center でのデータ収集](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="identity-and-access-control"></a>ID およびアクセス制御

*詳細については、[Azure セキュリティ ベンチマークの「ID およびアクセス制御](../../security/benchmarks/security-control-identity-access-control.md)」を参照してください。*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: 管理アカウントのインベントリを維持する

**ガイダンス**:Azure Active Directory (Azure AD) はユーザー アクセスを管理するための推奨される方法ですが、Azure Virtual Machines にもローカル アカウントが存在する可能性があります。 ローカル アカウントとドメイン アカウントの両方を、通常、最小の占有領域で、確認して管理する必要があります。 さらに、仮想マシンのリソースにアクセスするために使用される管理者アカウントには Azure Privileged Identity Management を利用します。

- [ローカル アカウントに関する情報](https://docs.microsoft.com/azure/active-directory/devices/assign-local-admin#manage-the-device-administrator-role)

- [Privileged Identity Manager に関する情報](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="32-change-default-passwords-where-applicable"></a>3.2: 既定のパスワードを変更する (該当する場合)

**ガイダンス**: Windows Virtual Machines と Azure Active Directory (Azure AD) には、既定のパスワードという概念がありません。 既定のパスワードを使用する可能性のあるサード パーティ アプリケーションやマーケットプレース サービスに対する責任は、お客様が負います。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: 専用管理者アカウントを使用する

**ガイダンス**:仮想マシンにアクセスできる専用管理者アカウントの使用に関する標準的な操作手順を作成します。 Azure Security Center ID 管理とアクセス管理を使用して、管理者アカウントの数を監視します。 Azure 仮想マシンのリソースにアクセスするために使用される管理者アカウントはすべて、Azure Privileged Identity Management (PIM) でも管理できます。 Azure Privileged Identity Management には、いくつかのオプションが用意されています。たとえば、ロールを引き継ぐ前に多要素認証を要求する Just-In-Time 昇格や、特定の期間のみアクセス許可が使用でき、かつ承認者を要求するようにする委任オプションです。

- [Azure Security Center ID とアクセスについて](../../security-center/security-center-identity-access.md)

- [Privileged Identity Manager に関する情報](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 3.3](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-3-3.md)]

### <a name="34-use-azure-active-directory-single-sign-on-sso"></a>3.4: Azure Active Directory シングル サインオン (SSO) を使用する

**ガイダンス**: 可能な限り、顧客はサービスごとに個別のスタンドアロン資格情報を構成するのではなく、SSO を Azure Active Directory (Azure AD) と一緒に使用します。 Azure Security Center ID とアクセス管理の推奨事項を使用してください。

- [Azure AD でのアプリケーションへのシングル サインオン](../../active-directory/manage-apps/what-is-single-sign-on.md)

- [Azure Security Center で ID とアクセスを監視する方法](../../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="35-use-multi-factor-authentication-for-all-azure-active-directory-based-access"></a>3.5: すべての Azure Active Directory ベースのアクセスに多要素認証を使用する

**ガイダンス**: Azure Active Directory (Azure AD) の多要素認証を有効にし、Azure Security Center の ID およびアクセス管理の推奨事項に従います。

- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

- [Azure Security Center で ID とアクセスを監視する方法](../../security-center/security-center-identity-access.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="36-use-secure-azure-managed-workstations-for-administrative-tasks"></a>3.6: セキュリティで保護された Azure マネージド ワークステーションを管理タスクに使用する 

**ガイダンス**: 多要素認証が構成された PAW (特権アクセス ワークステーション) を使用して Azure リソースにログインし、そのリソースを構成します。

- [特権アクセス ワークステーションについて](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Azure で多要素認証を有効にする方法](../../active-directory/authentication/howto-mfa-getstarted.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="37-log-and-alert-on-suspicious-activities-from-administrative-accounts"></a>3.7: 管理者アカウントからの疑わしいアクティビティに関するログとアラート

**ガイダンス**: Azure Active Directory (Azure AD) Privileged Identity Management (PIM) を使用して、環境内で疑わしいアクティビティまたは安全ではないアクティビティが発生したときにログとアラートを生成します。 Azure AD のリスク検出を使用して、危険なユーザーの行動に関するアラートとレポートを表示します。 必要に応じて、顧客は Azure Security Center のリスク検出アラートを Azure Monitor に取り込み、アクション グループを使用してカスタムのアラート生成/通知を構成できます。

- [Privileged Identity Management (PIM) をデプロイする方法](../../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Azure Security Center のリスク検出 (疑わしいアクティビティ) について](../../active-directory/identity-protection/overview-identity-protection.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [カスタムのアラートおよび通知用にアクション グループを構成する方法](../../azure-monitor/alerts/action-groups.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="38-manage-azure-resources-from-only-approved-locations"></a>3.8:承認された場所からのみ Azure リソースを管理する

**ガイダンス**: IP アドレス範囲または国や地域の特定の論理グループからのアクセスのみを許可するには、Azure Active Directory (Azure AD) の条件付きアクセス ポリシーとネームド ロケーションを使用します。

- [Azure でネームド ロケーションを構成する方法](../../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="39-use-azure-active-directory"></a>3.9: Azure Active Directory を使用する

**ガイダンス**: Azure Active Directory (Azure AD) を中央認証と承認システムとして使用します。 Azure AD でデータを保護するには、保存データと転送中のデータに強力な暗号化を使用します。 また、Azure AD では、ユーザーの資格情報がソルト化およびハッシュされ、安全に格納されます。  マネージド ID を使用すると、コード内に資格情報を記述しなくても、Azure AD 認証をサポートするすべてのサービス (Key Vault を含む) に対して認証できます。 仮想マシン上で実行されているコードは、そのマネージド ID を使用して、Azure AD 認証をサポートするサービスのアクセス トークンを要求できます。

- [Azure AD インスタンスを作成して構成する方法](../../active-directory-domain-services/tutorial-create-instance.md)

- [Azure リソースのマネージド ID の概要](../../active-directory/managed-identities-azure-resources/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="310-regularly-review-and-reconcile-user-access"></a>3.10: ユーザー アクセスを定期的に確認して調整する

**ガイダンス**: Azure Active Directory (Azure AD) では、古いアカウントの検出に役立つログが提供されます。 また、Azure AD の ID アクセス レビューを使用して、グループ メンバーシップ、エンタープライズ アプリケーションへのアクセス、およびロールの割り当てを効率的に管理します。 ユーザーのアクセスを定期的にレビューすると、適切なユーザーのみが継続的なアクセス権を持っていることを確認できます。 Azure 仮想マシンを使用している場合は、ローカル セキュリティ グループとユーザーをレビューして、システムを危険にさらす可能性のある予期しないアカウントが存在しないことを確認する必要があります。

- [Azure ID アクセス レビューの使用方法](../../active-directory/governance/access-reviews-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="311-monitor-attempts-to-access-deactivated-credentials"></a>3.11: 非アクティブ化された資格情報へのアクセスの試行を監視する

**ガイダンス**: 監査ログとサインイン ログを Log Analytics ワークスペースに送信するように Azure Active Directory (Azure AD) の診断設定を構成します。 また、ログを確認したり、Azure 仮想マシンのログ データにクエリを実行したりするには Azure Monitor を使用します。

- [Log Analytics ワークスペースについて](../../azure-monitor/logs/log-analytics-tutorial.md)

- [Azure アクティビティ ログを Azure Monitor に統合する方法](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

- [Azure Monitor でカスタム クエリを実行する方法](../../azure-monitor/logs/get-started-queries.md)

- [Azure で仮想マシンを監視する方法](../../azure-monitor/vm/monitor-vm-azure.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="312-alert-on-account-sign-in-behavior-deviation"></a>3.12: アカウント サインイン動作の偏差に関するアラートを生成する

**ガイダンス**: ストレージ アカウント リソースに関連して検出された疑わしいアクションへの自動応答を構成するには、Azure Active Directory (Azure AD) のリスクおよび Identity Protection 機能を使用します。 組織のセキュリティ対応を実装するには、Azure Sentinel によって自動応答を有効にする必要があります。

- [Azure AD の危険なサインインを表示する方法](../../active-directory/identity-protection/overview-identity-protection.md)

- [Identity Protection のリスク ポリシーを構成して有効にする方法](../../active-directory/identity-protection/howto-identity-protection-configure-risk-policies.md)

- [Azure Sentinel をオンボードする方法](../../sentinel/quickstart-onboard.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="313-provide-microsoft-with-access-to-relevant-customer-data-during-support-scenarios"></a>3.13: サポート シナリオで関連する顧客データに Microsoft がアクセスできるようにする

**ガイダンス**:サード パーティが顧客データにアクセスする必要がある場合 (サポート リクエスト中など) は、Azure 仮想マシンのカスタマー ロックボックスを使用して、顧客データへのアクセス要求を確認して承認または拒否します。

- [カスタマー ロックボックスについて](../../security/fundamentals/customer-lockbox-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="data-protection"></a>データ保護

*詳細については、[Azure セキュリティ ベンチマークの「データ保護](../../security/benchmarks/security-control-data-protection.md)」を参照してください。*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: 機密情報のインベントリを維持する

**ガイダンス**: 機密情報を格納または処理する Azure 仮想マシンを追跡しやすくするには、タグを使用します。

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2:機密情報を格納または処理するシステムを分離する

**ガイダンス**:開発、テスト、および運用で別々のサブスクリプションまたは管理グループ、あるいはその両方を実装します。 リソースは、仮想ネットワーク/サブネットで分離し、適切にタグ付けして、ネットワーク セキュリティ グループ (NSG) 内または Azure Firewall でセキュリティ保護する必要があります。 機密データを格納または処理する仮想マシンでは、使用されていないときにはオフにするためのポリシーとプロシージャを実装します。

- [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

- [仮想ネットワークを作成する方法](../../virtual-network/quick-create-portal.md)

- [セキュリティ構成を使用して NSG を作成する方法](../../virtual-network/tutorial-filter-network-traffic.md)

- [Azure Firewall をデプロイする方法l](../../firewall/tutorial-firewall-deploy-portal.md)

- [Azure Firewall でアラートまたはアラートと拒否を構成する方法](../../firewall/threat-intel.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3:機密情報の承認されていない転送を監視してブロックする

**ガイダンス**: 機密情報の承認されていない転送を監視し、このような転送をブロックしながら情報セキュリティ担当者にアラートを送信するサード パーティ ソリューションをネットワーク境界に実装します。

Microsoft によって管理される基になるプラットフォームの場合、Microsoft は顧客データを損失や漏洩から保護するために、顧客のすべてのコンテンツを機密として扱います。 Azure 内の顧客データが確実にセキュリティで保護されるように、Microsoft では一連の堅牢なデータ保護制御および機能を実装して管理しています。

- [Azure での顧客データの保護について](../../security/fundamentals/protection-customer-data.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4:転送中のすべての機密情報を暗号化する

**ガイダンス**: Windows を実行している仮想マシン (VM) が転送先または転送元である転送中のデータと、それらのマシン間で転送中のデータは、RDP または SSH セッションで VM に接続する場合など、接続の性質に応じてさまざまな方法で暗号化されます。

Microsoft では、トランスポート層セキュリティ (TLS) プロトコルを使用して、クラウド サービスと顧客の間で転送されているデータを保護します。

- [VM での転送中の暗号化](https://docs.microsoft.com/azure/security/fundamentals/encryption-overview#in-transit-encryption-in-vms)

**責任**: 共有

**Azure Security Center の監視**: なし

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5:アクティブ検出ツールを使用して機密データを特定する

**ガイダンス**:サードパーティのアクティブ検出ツールを使用して、組織のテクノロジ システムによって格納、処理、または送信されるすべての機密情報 (オンサイトにあるもの、またはリモート サービス プロバイダーにあるものなど) を特定し、組織の機密情報インベントリを更新します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="46-use-azure-rbac-to-control-access-to-resources"></a>4.6:Azure RBAC を使用してリソースへのアクセスを制御する 

**ガイダンス**: Azure ロールベースのアクセス制御 (Azure RBAC) を使用すると、チーム内の職務を分離し、VM 上のユーザーに自分の職務を実行するために必要なアクセス権のみを付与できます。 すべてのユーザーに VM への無制限のアクセス許可を付与するのではなく、特定の操作のみを許可することができます。 Azure CLI または Azure PowerShell を使用して、Azure portal で VM のアクセス制御を構成できます。

- [Azure RBAC](../../role-based-access-control/overview.md)

- [Azure 組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#virtual-machine-contributor)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7:ホストベースのデータ損失防止を使用してアクセス制御を実施する

**ガイダンス**: データ侵害のリスクを軽減するためのアクセス制御を実施するには、サード パーティ製ツール (自動化されたホスト ベースのデータ損失防止ソリューションなど) を実装します。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8:機密情報を保存時に暗号化する

**ガイダンス**: Windows Virtual Machines (VM) 上の仮想ディスクは、サーバー側暗号化または Azure Disk Encryption (ADE) のどちらかを使用して、保存時に暗号化されます。 Azure Disk Encryption では、Windows の BitLocker 機能を利用して、ゲスト VM 内のカスタマー マネージド キーを使用してマネージド ディスクを暗号化します。 カスタマー マネージド キーを使用したサーバー側の暗号化では、ストレージ サービス内のデータを暗号化することで、VM に対して任意の OS の種類とイメージを使用できるため、ADE がさらに向上します。

- [Azure Managed Disks のサーバー側暗号化](/azure/virtual-machines/windows/disk-encryption)

- [Windows VM 用の Azure Disk Encryption](/azure/virtual-machines/windows/disk-encryption-overview)

**責任**: 共有

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-4-8.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 4.8](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-4-8.md)]

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9:重要な Azure リソースへの変更に関するログとアラート

**ガイダンス**:仮想マシンや関連リソースにいつ変更が加えられたかに関するアラートを作成するには、Azure Monitor を Azure アクティビティ ログと共に使用します。  

- [Azure アクティビティ ログ イベントのアラートを作成する方法](../../azure-monitor/alerts/alerts-activity-log.md)

- [Azure Storage Analytics のログ](../../storage/common/storage-analytics-logging.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="vulnerability-management"></a>脆弱性の管理

*詳細については、[Azure セキュリティ ベンチマークの「脆弱性の管理](../../security/benchmarks/security-control-vulnerability-management.md)」を参照してください。*

### <a name="51-run-automated-vulnerability-scanning-tools"></a>5.1:自動化された脆弱性スキャン ツールを実行する

**ガイダンス**: Azure 仮想マシンに対する脆弱性評価の実行に関する Azure Security Center の推奨事項に従います。  仮想マシンの脆弱性評価を実行するには、Azure セキュリティの推奨されるソリューションまたはサードパーティ ソリューションを使用します。

- [Azure Security Center の脆弱性評価の推奨事項を実装する方法](../../security-center/deploy-vulnerability-assessment-vm.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-5-1.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.1](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-1.md)]

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2:自動化されたオペレーティング システム修正プログラム管理ソリューションを展開する

**ガイダンス**:仮想マシンの更新プログラムと修正プログラムを管理するには、Azure Update Management ソリューションを使用します。  Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。

- [Azure の Update Management ソリューション](/azure/automation/overview)

- [Azure VM の更新プログラムとパッチの管理](/azure/automation/manage-updates-for-vm)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 5.2](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-5-2.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.2](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-2.md)]

### <a name="53-deploy-automated-patch-management-solution-for-third-party-software-titles"></a>5.3:サード パーティ ソフトウェア タイトル用の自動化された修正プログラム管理ソリューションをデプロイする

**ガイダンス**: サード パーティ製の修正プログラム管理ソリューションを使用できます。  Azure Update Management のソリューションを使用すると、仮想マシンの更新プログラムと修正プログラムを管理できます。  Update Management は、サポート対象の Windows システムへの修正プログラムの適用を、ローカルに構成された更新リポジトリに依存しています。 System Center Updates Publisher (Updates Publisher) などのツールを使用して、カスタム更新プログラムを Windows Server Update Services (WSUS) に公開できます。 このシナリオでは、サード パーティ製ソフトウェアで Configuration Manager を更新リポジトリとして使用するマシンに、Update Management で修正プログラムを適用できます。

- [Azure の Update Management ソリューション](/azure/automation/overview)

- [Azure VM の更新プログラムとパッチの管理](/azure/automation/manage-updates-for-vm)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="54-compare-back-to-back-vulnerability-scans"></a>5.4:バックツーバックの脆弱性スキャンを比較する

**ガイダンス**: スキャン結果を一定の間隔でエクスポートして結果を比較し、脆弱性が修復されていることを確認します。 Azure Security Center によって提案された脆弱性管理の推奨事項を使用する場合、お客様は選択したソリューションのポータルに切り替えてスキャン データの履歴を表示できます。

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5:リスク評価プロセスを使用して、検出された脆弱性の修復に優先順位を付ける

**ガイダンス**: Azure Security Center によって提供される既定のリスク評価 (セキュリティ スコア) を使用します。

- [Azure Security Center のセキュリティ スコアについて](../../security-center/secure-score-security-controls.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-5-5.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 5.5](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-5-5.md)]

## <a name="inventory-and-asset-management"></a>インベントリと資産の管理

*詳細については、[Azure セキュリティ ベンチマークの「インベントリと資産の管理](../../security/benchmarks/security-control-inventory-asset-management.md)」を参照してください。*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1:自動化された資産検出ソリューションを使用する

**ガイダンス**:サブスクリプション内のすべてのリソース (仮想マシンを含む) のクエリや検出を行うには、Azure Resource Graph を使用します。 テナント内の適切な (読み取り) アクセス許可を持っており、サブスクリプション内のリソースだけでなく、すべての Azure サブスクリプションを列挙できることを確認します。

- [Azure Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

- [Azure サブスクリプションを表示する方法](/powershell/module/az.accounts/get-azsubscription)

- [Azure RBAC について](../../role-based-access-control/overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="62-maintain-asset-metadata"></a>6.2:資産メタデータを保持する

**ガイダンス**: メタデータを提供する Azure リソースにタグを適用すると、それらのリソースが各分類に応じて論理的に整理されます。

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="63-delete-unauthorized-azure-resources"></a>6.3:承認されていない Azure リソースを削除する

**ガイダンス**:仮想マシンや関連リソースを整理したり追跡したりするには、必要に応じて、タグ付け、管理グループ、個別のサブスクリプションを使用します。 定期的にインベントリを調整し、承認されていないリソースがサブスクリプションから適切なタイミングで削除されるようにします。

- [追加の Azure サブスクリプションを作成する方法](../../cost-management-billing/manage/create-subscription.md)

- [管理グループを作成する方法](../../governance/management-groups/create-management-group-portal.md)

- [タグを作成して使用する方法](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="64-define-and-maintain-inventory-of-approved-azure-resources"></a>6.4:承認された Azure リソースのインベントリを定義および管理する

**ガイダンス**: 承認された Azure リソースと、コンピューティング リソースの承認されたソフトウェアのインベントリを作成する必要があります。 また、構成されたマシン グループでの実行が許可された一連のアプリケーションを定義するために役立つ、Azure Security Center の機能である適応型アプリケーション制御を使用することもできます。 この機能は、Azure と Azure 以外の Windows (すべてのバージョン、クラシック、または Azure Resource Manager) コンピューターおよび Linux コンピューターで使用できます。
                    

- [適応型アプリケーション制御を有効にする方法](../../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5:承認されていない Azure リソースを監視する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類

- 許可されるリソースの種類

また、Azure Resource Graph を使用すると、サブスクリプション内のリソースのクエリまたは検出を行えます。 これは、高いセキュリティに基づいた環境 (ストレージ アカウントを使用するものなど) に役立ちます。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Graph を使用してクエリを作成する方法](../../governance/resource-graph/first-query-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6:コンピューティング リソース内の承認されていないソフトウェア アプリケーションを監視する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。 Azure 仮想マシンのインベントリを利用して、Virtual Machines 上のすべてのソフトウェアに関する情報の収集を自動化します。 注:ソフトウェアの名前、バージョン、発行元、および更新時刻は、Azure portal から入手できます。 インストール日やその他の情報にアクセスするには、お客様はゲストレベルの診断を有効にし、Windows イベント ログを Log Analytics ワークスペースに取り込む必要があります。

ソフトウェア アプリケーションの監視のための Change Tracking の使用に加えて、Azure Security Center の適応型アプリケーション制御では、マシン上で実行されているアプリケーションを分析し、このインテリジェンスから許可リストを作成するために機械学習を使用します。 この機能を使用すると、アプリケーション許可リスト ポリシーを構成して維持するプロセスが大幅に簡略化されるため、環境で望ましくないソフトウェアが使用されることを回避できます。 監査モードまたは強制モードを構成できます。 監査モードでは、保護された VM に対するアクティビティが監査されるだけです。 強制モードでは規則が強制され、実行が許可されていないアプリケーションが確実にブロックされます。

- [Azure Automation の概要](../../automation/automation-intro.md)

- [Azure VM インベントリを有効にする方法](../../automation/automation-tutorial-installed-software.md)

- [適応型アプリケーション制御の概要](../../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7:承認されていない Azure リソースとソフトウェア アプリケーションを削除する

**ガイダンス**: Azure Automation は、ワークロードとリソースの展開、運用、および使用停止を完全に制御します。  Change Tracking を使用して、Virtual Machines にインストールされているすべてのソフトウェアを特定できます。 独自のプロセスを実装するか、Azure Automation State Configuration を使用して、承認されていないソフトウェアを削除できます。

- [Azure Automation の概要](../../automation/automation-intro.md)

- [Change Tracking ソリューションを使用して環境内の変更を追跡する](../../automation/change-tracking/overview.md)

- [Azure Automation State Configuration の概要](../../automation/automation-dsc-overview.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="68-use-only-approved-applications"></a>6.8:承認されたアプリケーションのみを使用する

**ガイダンス**: Azure Security Center の適応型アプリケーション制御を使用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにします。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 6.8](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-6-8.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.8](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-8.md)]

### <a name="69-use-only-approved-azure-services"></a>6.9:承認された Azure サービスのみを使用する

**ガイダンス**:次の組み込みのポリシー定義を使用して、顧客のサブスクリプション内に作成できるリソースの種類に制限を適用するには、Azure Policy を使用します。
- 許可されないリソースの種類

- 許可されるリソースの種類

詳細については、次のリファレンスを参照してください。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy を使用して特定のリソースの種類を拒否する方法](https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#general)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-6-9.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.9](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-9.md)]

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10:承認されたソフトウェア タイトルのインベントリを管理する

**ガイダンス**: 適応型アプリケーション制御は、お使いの Azure および Azure 以外のコンピューター (Windows および Linux) 上でどのアプリケーションが実行できるかを制御できる、Azure Security Center によるインテリジェントかつ自動化されたエンドツーエンドのソリューションです。 これが組織の要件を満たしていない場合は、サードパーティ ソリューションを実装します。

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 6.10](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-6-10.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 6.10](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-6-10.md)]

### <a name="611-limit-users-ability-to-interact-with-azure-resource-manager"></a>6.11:Azure Resource Manager を操作するユーザーの機能を制限する

**ガイダンス**:"Microsoft Azure Management" アプリに対して [アクセスのブロック] を構成することによって、Azure Resource Manager を操作するユーザーの機能を制限するには、Azure 条件付きアクセスを使用します。

- [Azure Resource Manager へのアクセスをブロックするように条件付きアクセスを構成する方法](../../role-based-access-control/conditional-access-azure-management.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="612-limit-users-ability-to-execute-scripts-within-compute-resources"></a>6.12:コンピューティング リソース内でスクリプトを実行するユーザーの機能を制限する

**ガイダンス**: スクリプトの種類に応じて、オペレーティング システム固有の構成またはサードパーティのリソースを使用して、ユーザーが Azure コンピューティング リソース内でスクリプトを実行する機能を制限できます。 また、Azure Security Center の適応型アプリケーション制御を利用して、Azure Virtual Machines 上で承認されたソフトウェアのみを実行し、すべての承認されていないソフトウェアが実行されないようにすることもできます。

- [Windows 環境で PowerShell スクリプトの実行を制御する方法](/powershell/module/microsoft.powershell.security/set-executionpolicy)

- [Azure Security Center の適応型アプリケーション制御を使用する方法](../../security-center/security-center-adaptive-application.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13:リスクの高いアプリケーションを物理的または論理的に分離する

**ガイダンス**: Azure 環境にデプロイされた高リスク アプリケーションは、仮想ネットワーク、サブネット、サブスクリプション、管理グループなどを使用して分離し、Azure Firewall、Web アプリケーション ファイアウォール (WAF)、またはネットワーク セキュリティ グループ (NSG) によって十分に保護できます。

- [Azure における仮想ネットワークと仮想マシン](/azure/virtual-machines/windows/network-overview)

- [Azure Firewall の概要](../../firewall/overview.md)

- [Web アプリケーション ファイアウォールの概要](../../web-application-firewall/overview.md)

- [ネットワーク セキュリティの概要](../../virtual-network/network-security-groups-overview.md)

- [Azure Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)

- [Azure 管理グループでリソースを整理する](../../governance/management-groups/overview.md)

- [サブスクリプション決定ガイド](/azure/cloud-adoption-framework/decision-guides/subscriptions/)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="secure-configuration"></a>セキュリティで保護された構成

*詳細については、[Azure セキュリティ ベンチマークの「セキュリティで保護された構成](../../security/benchmarks/security-control-secure-configuration.md)」を参照してください。*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1:すべての Azure リソースに対してセキュリティで保護された構成を確立する

**ガイダンス**: Azure Policy または Azure Security Center を使用して、すべての Azure リソースのセキュリティ構成を維持します。 また、Azure Resource Manager には、テンプレートを JavaScript Object Notation (JSON) でエクスポートする機能があります。これは、構成が会社のセキュリティ要件を確実に満たすか、それを超えるようにするために確認する必要があります。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [VM テンプレートをダウンロードする方法に関する情報](/previous-versions/azure/virtual-machines/windows/download-template)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="72-establish-secure-operating-system-configurations"></a>7.2:セキュリティで保護されたオペレーティング システムの構成を確立する

**ガイダンス**: すべてのコンピューティング リソースのセキュリティ構成を維持するには、Azure Security Center の推奨事項 [Remediate Vulnerabilities in Security Configurations on your Virtual Machines]\(仮想マシンのセキュリティ構成の脆弱性を修復する\) を使用します。

- [Azure Security Center の推奨事項を監視する方法](../../security-center/security-center-recommendations.md)

- [Azure Security Center のレコメンデーションを修復する方法](../../security-center/security-center-remediate-recommendations.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3:セキュリティで保護された Azure リソースの構成を維持する

**ガイダンス**: 仮想マシンに関連付けられている Azure リソースを安全に構成するには、Azure Resource Manager テンプレートと Azure Policy を使用します。  Azure Resource Manager テンプレートは、仮想マシンを Azure リソースと共にデプロイするために使用される JSON ベースのファイルであり、カスタム テンプレートは維持される必要があります。  基本テンプレートの管理は、Microsoft が行います。  Azure リソース全体にセキュリティで保護された設定を適用するには、Azure ポリシー [拒否] と [存在する場合はデプロイする] を使用します。

- [Azure Resource Manager テンプレートの作成に関する情報](ps-template.md)

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy の効果について](../../governance/policy/concepts/effects.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="74-maintain-secure-operating-system-configurations"></a>7.4:セキュリティで保護されたオペレーティング システムの構成を維持する

**ガイダンス**: Azure Virtual Machines (VM) のセキュリティで保護された構成をデプロイ用に維持するためのオプションがいくつかあります。

1- Azure Resource Manager テンプレート: これらは、Azure portal から VM をデプロイするために使用される JSON ベースのファイルであり、カスタム テンプレートをメンテナンスする必要があります。 基本テンプレートの管理は、Microsoft が行います。

2- カスタム仮想ハード ディスク (VHD): 他の方法で管理できない複雑な環境を扱う場合など、状況によっては、カスタム VHD ファイルを使用することが必要な場合があります。

3- Azure Automation State Configuration: ベース OS がデプロイされたら、これを使用して設定をより細かく制御し、オートメーション フレームワークを通じて適用できます。

ほとんどのシナリオで、Microsoft ベース VM テンプレートと Azure Automation Desired State Configuration を組み合わせると、セキュリティ要件を満たし、それを維持するために役立つ可能性があります。

- [VM テンプレートをダウンロードする方法に関する情報](/previous-versions/azure/virtual-machines/windows/download-template)

- [Resource Manager テンプレートの作成に関する情報](ps-template.md)

- [カスタム VM VHD を Azure にアップロードする方法](upload-generalized-managed.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 7.4](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-7-4.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.4](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-4.md)]

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5:Azure リソースの構成を安全に格納する

**ガイダンス**: カスタム Azure ポリシー、Azure Resource Manager テンプレート、Desired State Configuration スクリプトなど、ご利用のコードを安全に格納して管理するには、Azure Repos を使用します。Azure DevOps で管理するリソース (コード、ビルド、作業の追跡など) にアクセスするには、これらの特定のリソースに対するアクセス許可が必要です。 ほとんどのアクセス許可は、アクセス許可とアクセスに関するページで説明されている組み込みのセキュリティ グループを通して付与されます。 Azure DevOps と統合されている場合は Azure Active Directory (Azure AD)、または TFS と統合されている場合は Active Directory で定義されている特定のユーザー、組み込みのセキュリティ グループ、またはグループにアクセス許可を付与または拒否できます。

- [Azure DevOps でコードを格納する方法](/azure/devops/repos/git/gitworkflow)

- [Azure DevOps でのアクセス許可とグループについて](/azure/devops/organizations/security/about-permissions)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="76-securely-store-custom-operating-system-images"></a>7.6:カスタム オペレーティング システム イメージを安全に格納する

**ガイダンス**: カスタム イメージ (仮想ハード ディスクなど) を使用している場合は、Azure のロールベースのアクセス制御を使用して、承認されたユーザーのみがイメージにアクセスできるようにします。  

- [Azure RBAC について](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- [Azure RBAC を構成する方法](../../role-based-access-control/quickstart-assign-role-user-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7:Azure リソース用の構成管理ツールをデプロイする

**ガイダンス**:仮想マシンのシステム構成に関するアラート生成、監査、適用を行うには、Azure Policy を利用します。 さらに、ポリシー例外を管理するためのプロセスとパイプラインを作成します。

- [Azure Policy を構成して管理する方法](../../governance/policy/tutorials/create-and-manage.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8:オペレーティング システム用の構成管理ツールをデプロイする

**ガイダンス**:Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../../automation/automation-dsc-onboarding.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9:Azure リソースの自動構成監視を実装する

**ガイダンス**: Azure 仮想マシンのベースライン スキャンを実行するには、Azure Security Center を利用します。  自動構成のためのその他の方法として、Azure Automation State Configuration の使用があります。

- [Azure Security Center の推奨事項を修復する方法](../../security-center/security-center-remediate-recommendations.md)

- [Azure Automation State Configuration の使用開始](../../automation/automation-dsc-getting-started.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10:オペレーティング システムの自動構成監視を実装する

**ガイダンス**: Azure Automation State Configuration は、あらゆるクラウドまたはオンプレミスのデータセンターにある Desired State Configuration (DSC) ノードの管理構成サービスです。 これにより、一元化された安全な場所から、何千ものマシンでの迅速かつ簡単なスケーラビリティが可能になります。 マシンのオンボード、それらへの宣言型構成の割り当て、さらに指定した望ましい状態への各マシンの準拠を示すレポートの表示を容易に行うことができます。

- [Azure Automation State Configuration による管理のためのマシンのオンボード](../../automation/automation-dsc-onboarding.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 7.10](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-7-10.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 7.10](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-7-10.md)]

### <a name="711-manage-azure-secrets-securely"></a>7.11:Azure シークレットを安全に管理する

**ガイダンス**: マネージド サービス ID を Azure Key Vault と組み合わせて使用し、クラウド アプリケーションのシークレット管理を簡素化し、セキュリティで保護します。

- [Azure マネージド ID と統合する方法](../../azure-app-configuration/howto-integrate-azure-managed-service-identity.md)

- [キー コンテナーを作成する方法](../../key-vault/general/quick-create-portal.md)

- [Key Vault に対して認証を行う方法](../../key-vault/general/authentication.md)

- [Key Vault のアクセス ポリシーを割り当てる方法](../../key-vault/general/assign-access-policy-portal.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="712-manage-identities-securely-and-automatically"></a>7.12:ID を安全かつ自動的に管理する

**ガイダンス**: マネージド ID を使用して、Azure Active Directory (Azure AD) で自動的に管理される ID を Azure サービスに提供します。 マネージド ID を使用すると、コードに資格情報を追加しなくても、Azure AD の認証をサポートするさまざまなサービス (Key Vault を含む) に対して認証を行うことができます。

- [マネージド ID を構成する方法](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13:意図しない資格情報の公開を排除する

**ガイダンス**: コード内で資格情報を特定する資格情報スキャナーを実装します。 また、資格情報スキャナーを使うと、検出された資格情報を、Azure Key Vault などのより安全な場所に移動しやすくなります。

- [資格情報スキャナーを設定する方法](https://secdevtools.azurewebsites.net/helpcredscan.html)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="malware-defense"></a>マルウェアからの防御

*詳細については、[Azure セキュリティ ベンチマークの「マルウェアからの防御](../../security/benchmarks/security-control-malware-defense.md)」を参照してください。*

### <a name="81-use-centrally-managed-anti-malware-software"></a>8.1:一元管理されたマルウェア対策ソフトウェアを使用する

**ガイダンス**: Azure Windows 仮想マシンに対して Microsoft Antimalware を使用し、リソースの継続的な監視と防御を行います。

- [Cloud Services と Virtual Machines に対して Microsoft Antimalware を構成する方法](../../security/fundamentals/antimalware.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.ClassicCompute**:

[!INCLUDE [Resource Policy for Microsoft.ClassicCompute 8.1](../../../includes/policy/standards/asb/rp-controls/microsoft.classiccompute-8-1.md)]

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.1](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-1.md)]

### <a name="83-ensure-anti-malware-software-and-signatures-are-updated"></a>手順 8.3:マルウェア対策ソフトウェアと署名が確実に更新されるようにする

**ガイダンス**: Azure 用 Microsoft Antimalware をデプロイすると、既定で、最新の署名、プラットフォーム、エンジンの更新プログラムが自動的にインストールされます。 Azure Security Center の"計算とアプリ" の推奨事項に従って、すべてのエンドポイントが最新の署名を備え、最新の状態になっているようにします。 Azure Security Center と統合された Microsoft Defender Advanced Threat Protection サービスを使用して、ウイルスやマルウェアベースの攻撃のリスクを限定する追加のセキュリティによって、Windows OS の保護を強化できます。

- [Azure Cloud Services および Virtual Machines 向けの Microsoft Antimalware をデプロイする方法](../../security/fundamentals/antimalware.md)

- [Microsoft Defender Advanced Threat Protection](/windows/security/threat-protection/microsoft-defender-atp/onboard-configure)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 8.3](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-8-3.md)]

## <a name="data-recovery"></a>データの復旧

*詳細については、[Azure セキュリティ ベンチマークの「データの復旧](../../security/benchmarks/security-control-data-recovery.md)」を参照してください。*

### <a name="91-ensure-regular-automated-back-ups"></a>9.1:定期的な自動バックアップを保証する

**ガイダンス**: Azure Backup を有効にし、Azure 仮想マシン (VM) のほか、自動バックアップの目的の頻度と保有期間を構成します。

- [Azure VM バックアップの概要](../../backup/backup-azure-vms-introduction.md)

- [VM の設定から Azure VM をバックアップする](../../backup/backup-azure-vms-first-look-arm.md)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.1](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-1.md)]

### <a name="92-perform-complete-system-backups-and-backup-any-customer-managed-keys"></a>9.2: システムの完全バックアップを実行し、すべてのカスタマー マネージド キーをバックアップする

**ガイダンス**: PowerShell または REST API を使用して、Azure 仮想マシン、またはこれらのインスタンスにアタッチされているマネージド ディスクのスナップショットを作成します。 Azure Key Vault 内のすべてのカスタマー マネージド キーをバックアップします。

Azure Backup を有効にし、Azure Virtual Machines (VM) のほか、目的の頻度と保有期間をターゲットとします。 これには、完全なシステム状態のバックアップが含まれます。 Azure Disk Encryption を使用している場合は、Azure VM バックアップによって、カスタマー マネージド キーのバックアップが自動的に処理されます。

- [暗号化を使用する Azure VM でのバックアップ](../../backup/backup-azure-vms-encryption.md)

- [Azure VM バックアップの概要](../../backup/backup-azure-vms-introduction.md)

- [Azure VM バックアップの概要](../../backup/backup-azure-vms-introduction.md)

- [Azure 上でキー コンテナーのキーをバックアップする方法](/powershell/module/az.keyvault/backup-azkeyvaultkey)

**責任**: Customer

**Azure Security Center の監視**: [Azure セキュリティ ベンチマーク](/azure/governance/policy/samples/azure-security-benchmark)は、Security Center の既定のポリシー イニシアチブであり、[Security Center の推奨事項](/azure/security-center/security-center-recommendations)の基礎となります。 このコントロールに関連する Azure Policy 定義は、Security Center によって自動的に有効になります。 この制御に関連するアラートでは、関連するサービスのために [Azure Defender](/azure/security-center/azure-defender) プランが必要になる場合があります。

**Azure Policy 組み込み定義 - Microsoft.Compute**:

[!INCLUDE [Resource Policy for Microsoft.Compute 9.2](../../../includes/policy/standards/asb/rp-controls/microsoft.compute-9-2.md)]

### <a name="93-validate-all-backups-including-customer-managed-keys"></a>9.3:カスタマー マネージド キーを含むすべてのバックアップを検証する

**ガイダンス**: Azure Backup 内でコンテンツのデータ復元を定期的に実行できるようにします。 必要に応じて、分離された仮想ネットワークまたはサブスクリプションへのコンテンツの復元をテストします。 顧客は、バックアップされたカスタマー マネージド キーの復元をテストします。

Azure Disk Encryption を使用している場合は、ディスク暗号化キーで Azure VM を復元できます。 ディスク暗号化を使用している場合は、ディスク暗号化キーで Azure VM を復元できます。

- [暗号化を使用する Azure VM でのバックアップ](../../backup/backup-azure-vms-encryption.md)

- [Azure 仮想マシンのバックアップからファイルを復旧する方法](../../backup/backup-azure-restore-files-from-vm.md)

- [Azure でキー コンテナーのキーを復元する方法](/powershell/module/az.keyvault/restore-azkeyvaultkey)

- [暗号化された VM をバックアップおよび復元する方法](../../backup/backup-azure-vms-encryption.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="94-ensure-protection-of-backups-and-customer-managed-keys"></a>9.4: バックアップとカスタマー マネージド キーの保護を保証する

**ガイダンス**: Azure Backup を使用して Azure マネージド ディスクをバックアップするとき、VM は Storage Service Encryption (SSE) を使用して保存時に暗号化されます。 Azure Backup では、Azure Disk Encryption を使用して暗号化されている Azure VM もバックアップできます。 Azure Disk Encryption は、シークレットとしてキー コンテナーで保護されている BitLocker 暗号化キー (BEK) と統合されます。 Azure Disk Encryption は、Azure Key Vault キー暗号化キー (KEK) とも統合されます。 Key Vault で論理的な削除を有効にして、偶発的または悪意のある削除からキーを保護します。

- [VM の論理的な削除](../../backup/soft-delete-virtual-machines.md)

- [Azure Key Vault の論理的な削除の概要](/azure/key-vault/general/overview-soft-delete)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="incident-response"></a>インシデント対応

*詳細については、[Azure セキュリティ ベンチマークの「インシデント対応](../../security/benchmarks/security-control-incident-response.md)」を参照してください。*

### <a name="101-create-an-incident-response-guide"></a>10.1:インシデント対応ガイドを作成する

**ガイダンス**: 組織のインシデント対応ガイドを作成します。 要員のすべてのロールを定義するインシデント対応計画が記述されていることと、検出からインシデント後のレビューまでのインシデント対応/管理のフェーズがあることを確認します。

- [独自のセキュリティ インシデント対応プロセスを構築するためのガイダンス](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process/)

- [Microsoft Security Response Center のインシデントの構造](https://msrc-blog.microsoft.com/2019/06/27/inside-the-msrc-anatomy-of-a-ssirp-incident/)

- [お客様は、独自のインシデント対応計画の作成に役立つ NIST の「コンピューター セキュリティ インシデント対応ガイド」を利用することもできます](https://csrc.nist.gov/publications/detail/sp/800-61/rev-2/final)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2:インシデントのスコアリングと優先順位付けの手順を作成する

**ガイダンス**: Security Center によって各アラートに重大度が割り当てられるため、最初に調査する必要があるアラートの優先順位付けに役立ちます。 重要度は、アラートの発行に使用された Security Center の信頼度と、アラートの原因となったアクティビティの背後に悪意のある意図があったかどうかの信頼レベルに基づいて決まります。

また、サブスクリプション ( 運用、非運用など) をタグを使用して明確にマークし、Azure リソース (特に、機密データを処理するもの) を明確に識別して分類するための命名システムを作成します。  インシデントが発生した Azure リソースと環境の重要度に基づいて、アラートの修復に優先順位を付けることは、お客様の責任です。

- [Security alerts in Azure Security Center](../../security-center/security-center-alerts-overview.md)

- [タグを使用した Azure リソースの整理](../../azure-resource-manager/management/tag-resources.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="103-test-security-response-procedures"></a>10.3:セキュリティ対応手順のテスト

**ガイダンス**:ご利用のシステムのインシデント対応機能を定期的にテストする演習を実施することで、ご利用の Azure リソースの保護を支援できます。 弱点やギャップを特定し、必要に応じて計画を見直します。

- [NIST の出版物「IT 計画と機能に関するテスト、トレーニング、演習プログラムのガイド」を参照してください。](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4:セキュリティ インシデントの連絡先の詳細を指定し、セキュリティ インシデントのアラート通知を構成します

**ガイダンス**:セキュリティ インシデントの連絡先情報は、Microsoft Security Response Center (MSRC) でユーザーのデータが違法または権限のないユーザーによってアクセスされたことが検出された場合に、Microsoft からの連絡先として使用されます。 事後にインシデントをレビューして、問題が解決されていることを確認します。

- [Azure Security Center のセキュリティ連絡先を設定する方法](../../security-center/security-center-provide-security-contact-details.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5:インシデント対応システムにセキュリティ アラートを組み込む

**ガイダンス**: 連続エクスポート機能を使用して Azure Security Center のアラートと推奨事項をエクスポートすると、Azure リソースへのリスクを特定できます。 連続エクスポートを使用すると、アラートと推奨事項を手動で、または継続した連続的な方法でエクスポートできます。 Azure Security Center データ コネクタを使用してアラートを Azure Sentinel にストリーミングできます。

- [連続エクスポートを構成する方法](../../security-center/continuous-export.md)

- [Azure Sentinel にアラートをストリーミングする方法](../../sentinel/connect-azure-security-center.md)

**責任**: Customer

**Azure Security Center の監視**: なし

### <a name="106-automate-the-response-to-security-alerts"></a>10.6:セキュリティ アラートへの対応を自動化する

**ガイダンス**: Azure Security Center のワークフロー自動化機能を使用すると、セキュリティのアラートと推奨事項に対して "Logic Apps" で自動的に応答をトリガーし、Azure リソースを保護できます。

- [ワークフローの自動化と Logic Apps を構成する方法](../../security-center/workflow-automation.md)

**責任**: Customer

**Azure Security Center の監視**: なし

## <a name="penetration-tests-and-red-team-exercises"></a>侵入テストとレッド チーム演習

*詳細については、[Azure セキュリティ ベンチマークの「侵入テストとレッド チーム演習](../../security/benchmarks/security-control-penetration-tests-red-team-exercises.md)」を参照してください。*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1:Azure リソースの通常の侵入テストを実施し、セキュリティに関する重大な調査結果がすべて、確実に修復されるようにする

**ガイダンス**: お客様の侵入テストが Microsoft のポリシーに違反しないように、Microsoft の実施ルールに確実に従ってください。 Microsoft が管理しているクラウド インフラストラクチャ、サービス、アプリケーションに対する Red Teaming およびライブ サイト侵入テストに関する Microsoft の戦略と実施を活用してください。

- [侵入テストの実施ルール](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1)

- [Microsoft Cloud Red Teaming](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**責任**: 共有

**Azure Security Center の監視**: なし

## <a name="next-steps"></a>次のステップ

- 「[Azure セキュリティ ベンチマーク V2 の概要](/azure/security/benchmarks/overview)」を参照してください。
- [Azure セキュリティ ベースライン](/azure/security/benchmarks/security-baselines-overview)の詳細について学習する
