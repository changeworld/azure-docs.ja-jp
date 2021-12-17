---
title: Azure Australia でのゲートウェイのログ記録、監査、可視性
description: オーストラリア リージョンのログ記録、監査、可視性を、オーストラリア政府のポリシー、規制、法令に固有の要件を満たすように構成する方法。
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 95d744785d95d5040e5d82d56d1905145111b682
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2021
ms.locfileid: "132340275"
---
# <a name="gateway-logging-auditing-and-visibility-in-azure-australia"></a>Azure Australia でのゲートウェイのログ記録、監査、可視性

サイバー セキュリティの脅威の検出と対応は、システムの運用に関連するデータの生成、収集、および分析に依存します。

Microsoft には、Azure にデプロイされたシステムのセキュリティを管理するためのログ記録、監査、可視性の実装に役立つ Azure の組み込みツールが用意されています。 また、Australian Cyber Security Centre (ACSC) Consumer Guidance と Information Security Manual (ISM) の意図に沿った参照アーキテクチャもあります。

ゲートウェイは、ネットワーク層の情報フロー制御メカニズムとして機能し、オープン システム相互接続 (OSI) モデルの上位層で情報を制御することもできます。 ゲートウェイは、セキュリティ ドメイン間のデータフローを制御し、外部ネットワークからの不正アクセスを防ぐために必要です。 セキュリティ ドメイン間の情報の流れの制御におけるゲートウェイの重要性により、あらゆる障害 (特に上位の分類) により重大な影響がもたらされる可能性があります。 そのため、サイバー セキュリティ インシデントを発生させる可能性がある状況を担当者に警告するための強力なメカニズムが、ゲートウェイにとって特に重要です。

ゲートウェイ用のログ記録機能とアラート機能を実装すると、サイバー セキュリティ インシデント、侵入の試み、および異常な使用パターンの検出に役立ちます。 さらに、イベント ログを別のセキュリティで保護されたログ サーバーに格納すると、敵対者が対象とするサイバー侵入の証拠を破壊するためにログ情報を削除することが難しくなります。

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Australian Cyber Security Centre (ACSC) の要件

連邦政府システムの全体的なセキュリティ要件は、ACSC Information Security Manual (ISM) で定義されています。 連邦機関が Azure 内でこれらの要件を満たすことができるように、『*ACSC コンシューマー ガイド – Azure at PROTECTED*』および『*ACSC 認定レポート – Microsoft Azure*』で、ログ記録、監査、可視性に関連する次の具体的な要件が詳しく説明されています。

1. 基になる共有クラウド リソースの使用に起因するリスクを軽減するため、連邦機関は、Azure Security Centre、Azure Monitor、Azure Policy、Azure Advisor などの Microsoft Azure によって提供される機能にオプトインし、機関が Azure ワークロードをリアルタイムに監視できるようにする必要があります

2. また、ACSC では、オーストラリア政府の監視のすべてについて、すべての必須セキュリティ ログを ACSC に転送することも、連邦機関に対して推奨されています

3. リスク軽減を支援するため、連邦機関は Azure サブスクリプション内で以下を構成する必要があります。

    * Azure Security Center を有効にする
    * Standard レベルにアップグレードする
    * サポートされる Azure VM に対して、Microsoft Monitoring Agent の自動プロビジョニングを有効にする
    * 定期的に、Security Center のダッシュボードで、セキュリティの推奨事項とアラートを確認し、優先順位を付け、軽減策を実施する

4. 政府機関は、Azure サブスクリプションから ACSC へのログとイベントの転送を有効にして、このガイダンスへの非準拠の可視性を ACSC に提供する必要があります。 Azure Event Hubs では、ACSC または連邦機関所有のオンプレミス システムに対して外部ログ ストリーミングを実行する機能が提供されています

5. 連邦機関は、Azure 内で有効にするログ記録を、ISM で指定された要件に合わせる必要があります

6. Microsoft では、Azure 内のログが 90 日間保持されます。 顧客エンティティはログ アーカイブ体制を導入し、NAA AFDA で必要とされる 7 年間、ログを保持できるようにする必要があります

7. オンプレミスまたは Azure ベースのセキュリティ情報およびイベント管理 (SIEM) 機能を持つ連邦機関は、それらのシステムにログを転送することもできます

8. コモンウェルス エンティティは、ネットワーク セキュリティ グループ (NSG) と仮想マシンに対して Network Watcher フロー ログを実装する必要があります。 これらのログは、セキュリティ ログのみが格納される専用のストレージ アカウントに格納する必要があります。また、ストレージ アカウントへのアクセスは Azure ロールベースのアクセス制御 (Azure RBAC) によって保護する必要があります

9. 連邦機関は ACSC コンシューマー ガイダンスを導入し、Azure ワークロードがログ記録と監視に関する ISM の意図を満たすようにする必要があります。 また、連邦機関は、Azure のオーストラリア政府による使用に関連するリアルタイムの監視、アラート、ログを受け取るために、ACSC をサポートする Azure の機能にオプトインする必要もあります

## <a name="architecture"></a>Architecture

Azure 環境に出入りするネットワーク トラフィックを確実に把握するため、適切なコンポーネントのセットで必要なログ記録を有効にする必要があります。 これを行うことにより、環境の完全な可視性が保証され、分析を行うために必要なデータが提供されます。

![Azure の監視アーキテクチャ](media/visibility.png)

## <a name="components"></a>Components

上で示したアーキテクチャは、ログ ソース、ログ収集、ログ保持、ログ分析、またはインシデント対応の機能を提供する個別のコンポーネントで構成されています。 このアーキテクチャには、インターネットにアクセス可能な Azure デプロイに通常含まれる個々のコンポーネントが含まれています。

|関数|Components|
|---|---|
|ログ ソース|<ul><li>Application Gateway</li><li>VPN Gateway</li><li>Azure Firewall</li><li>ネットワーク仮想アプライアンス</li><li>Azure Load Balancer</li><li>Virtual Machines</li><li>ドメイン ネーム システム (DNS) サーバー</li><li>Syslog またはログ収集サーバー</li><li>NSG</li><li>[Azure Activity Log (Azure アクティビティ ログ)]</li><li>Azure 診断ログ</li><li>Azure Policy</li></ul>|
|ログの収集|<ul><li>Event Hubs</li><li>Network Watcher</li><li>Log Analytics</li></ul>|
|ログ保持期間|<ul><li>Azure Storage</li></ul>|
| ログ分析|<ul><li>Microsoft Defender for Cloud</li><li>Azure Advisor</li><li>Log Analytics のソリューション<ul><li>Traffic Analytics</li><li>DNS Analytics (プレビュー)</li><li>アクティビティ ログ分析</li></ul></li><li>SIEM</li><li>ACSC</li></ul>|
|インシデント対応|<ul><li>Azure アラート</li><li>Azure Automation</li></ul>|
|

このアーキテクチャは、最初に必要なソースからログが生成された後、それらが集中リポジトリに収集されることで機能します。 収集されたログに対しては、次のことが可能になります。

* Azure Analysis Services によって分析情報を得るために使用される。
* 外部システムに転送される。
* 長期保存のためにストレージにアーカイブされる。

分析ツールによって識別された主要なイベントまたはインシデントに対応するため、アラートを構成し、予防的な管理と対応のために必要なアクションを実行するように自動化を開発できます。

## <a name="general-guidance"></a>一般的なガイダンス

この記事に記載されているコンポーネントを導入するにあたっては、次の一般的なガイダンスが適用されます。

* サービスが利用できるリージョンを確認し、すべてのデータが承認された場所内に維持されるようにします。また、保護されたワークロードについては、オーストラリア中部またはオーストラリア中部 2 を最優先のデプロイ先にします。

* 個々のサービスの認定状況については、ドキュメント『*Azure - ACSC 認定レポート - Protected 2018*』を参照してください。このレポートに記載のない関連コンポーネントについては、『*ACSC コンシューマー ガイド - Microsoft Azure at PROTECTED*』に従って自己評価を実施します。

* この記事で参照されていないコンポーネントの場合、連邦機関はログの生成、キャプチャ、分析、および保持に関する原則に従う必要があります

* 高価値システムおよび Azure でホストされているシステムへのすべてのネットワーク イングレスおよびエグレス ポイントにおいて、ログ記録、監査、可視性を識別して優先度を決定します

* ログを統合し、ストレージ アカウント、Log Analytics ワークスペース、Event Hubs などのログ ツール インスタンスの数を最小にします

* Azure ロールベースのアクセス制御 (Azure RBAC) によって管理特権を制限します

* Azure のリソースを管理または構成するアカウントに Multi-factor Authentication (MFA) を使用します

* 複数のサブスクリプションについてログ収集を一元化する場合は、管理者に各サブスクリプションで必要な特権があることを確認します

* ネットワーク仮想アプライアンス (NVA)、ログ収集サーバー、DNS サーバーなどの仮想マシンにネットワーク接続と必要なプロキシを構成し、Log Analytics ワークスペース、Event Hubs、ストレージなどの必要な Azure サービスに接続します

* TLS バージョン1.2 を利用するように Microsoft Monitoring Agent (MMA) を構成します

* Azure Policy を使用して要件への準拠を監視し、徹底します。

* ストレージやデータベースなどのすべてのデータ リポジトリに暗号化を適用します

* ローカル冗長ストレージ (LRS) とスナップショットを使用して、ストレージ アカウントと関連データの可用性を確保します

* ディザスター リカバリー戦略に合わせて、geo 冗長ストレージ (GRS) またはオフサイト ストレージを検討します

|リソース|URL|
|---|---|
|オーストラリアの規制とポリシーのコンプライアンス ドキュメント|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 製品 - オーストラリアのリージョンと非リージョン|[https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Microsoft Azure Security and Audit ログの管理ホワイトペーパー|[https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf](https://download.microsoft.com/download/B/6/C/B6C0A98B-D34A-417C-826E-3EA28CDFC9DD/AzureSecurityandAuditLogManagement_11132014.pdf)|
|Microsoft Monitoring Agent の構成|[https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent](../azure-monitor/agents/log-analytics-agent.md)|
|

## <a name="component-guidance"></a>コンポーネントのガイダンス

このセクションでは、ログ、監査、可視性アーキテクチャ全体における各コンポーネントの目的とその役割について取り上げます。 リファレンス ドキュメント、ガイド、チュートリアルなど参考になるリソースへのリンクも別途掲載しています。

## <a name="log-sources"></a>ログ ソース

分析、警告、またはレポートを完了する前に、必要なログを生成する必要があります。 Azure のログは、制御/管理ログ、データ プレーン ログ、処理済みイベントに分類されます。

|型|説明|
|---|---|
|制御/管理ログ|Azure Resource Manager の操作に関する情報が提供されます|
|データ プレーン ログ|仮想マシンのログや、Azure Monitor で使用できる診断ログなど、Azure リソースの使用状況の一部として発生するイベントに関する情報が提供されます|
|処理済みイベント|Azure によって処理された分析済みのイベント/アラートに関する情報が提供されます。たとえば、Microsoft Defender for Cloud ではサブスクリプションが処理および分析されて、セキュリティ アラートが提供されます|
|

### <a name="application-gateway"></a>Application Gateway

Azure Application Gateway は Azure 環境への有効なエントリ ポイントの 1 つであるため、Web アプリケーションと通信する受信接続に関する情報をキャプチャする必要があります。 Application Gateway では、Web アプリケーションの使用状況に関連し、サイバー セキュリティ インシデントの検出に役立つ重要な情報を、提供することができます。 Application Gateway から Azure Monitor のアクティビティ ログと診断ログに送信されたメタデータは、Log Analytics で利用したり、イベント ハブやストレージ アカウントに配布したりすることができます。

|リソース|リンク|
|---|---|
|Application Gateway のドキュメント|[https://docs.microsoft.com/azure/application-gateway/](../application-gateway/index.yml)|
|Application Gateway クイックスタート ガイド|[https://docs.microsoft.com/azure/application-gateway/quick-create-portal](../application-gateway/quick-create-portal.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

この VPN Gateway は、オンプレミス環境への接続や管理トラフィックなど、Azure 環境への幅広い通信のためのエントリ ポイントです。 VPN Gateway でのログ記録では、Azure 環境に対して行われた接続の分析情報と追跡可能性が提供されます。 ログ記録では、監査と分析を提供するだけでなく、悪意のある接続や異常な接続の検出や調査を支援することもできます。 VPN Gateway のログは、Azure Monitor アクティビティ ログに送信され、Log Analytics で利用したり、イベント ハブまたはストレージ アカウントに配布したりできます。

|リソース|リンク|
|---|---|
|VPN Gateway のドキュメント|[https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|オーストラリア政府固有の VPN Gateway ガイダンス|[Azure VPN Gateway の構成](vpn-gateway.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall では Azure 環境からの制御された終了ポイントが提供され、生成されるログ (送信接続の試行と成功に関する情報が含まれます) はログ戦略での重要な要素です。 これらのログは、システムが設計どおりに動作していることを検証したり、悪意のあるコードやアクターによる不正な外部システムへの接続の試みを検出したりするのに役立ちます。 Azure Firewall から Azure Monitor のアクティビティ ログと診断ログに書き込まれたログは、Log Analytics で利用したり、イベント ハブやストレージ アカウントに配布したりすることができます。

|リソース|リンク|
|---|---|
|Azure Firewall のドキュメント|[https://docs.microsoft.com/azure/firewall/](../firewall/index.yml)|
|チュートリアル: Azure Firewall のログとメトリックを監視する|[https://docs.microsoft.com/azure/firewall/tutorial-diagnostics](../firewall/firewall-diagnostics.md)|
|

### <a name="network-virtual-appliances-nva"></a>ネットワーク仮想アプライアンス (NVA)

NVA を使用して、Azure でネイティブに使用できるセキュリティ機能を補完することができます。 NVA で生成されるログは、サイバー セキュリティ インシデントを検出するうえで貴重なリソースになり、ログ記録、監査、および可視性戦略全体の重要な部分です。 NVA からのログをキャプチャするには、Microsoft Monitoring Agent (MMA) を利用します。 MMA のインストールがサポートされていない NVA の場合は、Syslog または他のログ収集サーバーを使用してログを中継することを検討してください。

|リソース|リンク|
|---|---|
|ネットワーク仮想アプライアンスの概要|[https://azure.microsoft.com/solutions/network-appliances](https://azure.microsoft.com/solutions/network-appliances)|
|NVA のドキュメント|Azure での関連する NVA の実装で、ベンダーのドキュメントを参照してください|
|

### <a name="azure-load-balancer"></a>Azure Load Balancer

Azure Load Balancer のログは、Azure にデプロイされたシステムに関連する接続と使用状況に関する有用な情報を取得するために使用されます。 これは、正常性と可用性の監視に使用できますが、通信トラフィックに関する必要な分析情報を取得し、悪意のあるトラフィック パターンや異常なトラフィック パターンを検出するための、もう 1 つの重要なコンポーネントも形成されます。 Azure Load Balancer から Azure Monitor のアクティビティ ログと診断ログに記録されたログは、Log Analytics で利用したり、イベント ハブやストレージ アカウントに配布したりすることができます。

|リソース|リンク|
|---|---|
|Azure Load Balancer のドキュメント|[https://docs.microsoft.com/azure/load-balancer](../load-balancer/index.yml)|
|Standard Load Balancer のメトリックと正常性の診断|[https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-diagnostics](../load-balancer/load-balancer-standard-diagnostics.md)|
|

### <a name="virtual-machines"></a>仮想マシン

仮想マシンは、ネットワーク通信を送受信し、データを処理し、サービスを提供するエンドポイントです。 仮想マシンではデータまたは重要なシステム サービスをホストできるため、それらが正常に動作しており、サイバー セキュリティ インシデントを検出しているようにすることが非常に重要になる場合があります。 仮想マシンでは、システムの動作およびそのシステムで実行された操作を追跡できる、さまざまなイベント ログや監査ログが収集されます。 仮想マシンで収集されたログは、Log Analytics エージェントを使用して Log Analytics ワークスペースに転送でき、そこで Microsoft Defender for Cloud によって分析できます。 さらに仮想マシンは、直接的に、またはログ収集サーバーを介して、Azure Event Hubs または SIEM ソリューションと直接統合することもできます。

|リソース|リンク|
|---|---|
|Virtual Machines|[https://docs.microsoft.com/azure/virtual-machines](../virtual-machines/index.yml)|
|Virtual Machines からデータを収集する|[https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm](../azure-monitor/vm/monitor-virtual-machine.md)|
|仮想マシンのログを Event Hubs にストリーミングする|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics-streaming-event-hubs](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)|
|

### <a name="domain-name-services-dns-servers"></a>ドメイン ネーム サービス (DNS) サーバー

DNS サーバーのログでは、システムが内部的または外部的にアクセスを試みるサービスに関連する重要な情報が提供されます。 DNS ログをキャプチャすることで、サイバー セキュリティ インシデントを特定し、インシデントの種類と、影響を受ける可能性のあるシステムについての分析情報を得ることができます。 Microsoft 管理エージェント (MMA) を DNS サーバーで使用して、DNS Analytics (プレビュー) で使用するためにログを Log Analytics に転送できます。

|リソース|リンク|
|---|---|
|仮想ネットワークの Azure 名前解決|[https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)|
|

### <a name="syslog-and-log-collection-servers"></a>Syslog およびログ収集サーバー

SIEM 内で使用するために、ネットワーク仮想アプライアンスから、または他のシステムからカスタム セキュリティ ログを受信するには、Azure VNet 内に専用サーバーをデプロイできます。 Syslog のログは、Syslog サーバーで収集し、分析のために Log Analytics に中継できます。 ログ収集サーバーは、集中監視システムまたは SIEM によって使用されるログの集計および配布機能に関する一般的な用語です。 これらを使用すると、ネットワークのアーキテクチャとセキュリティを簡素化し、中央の機能に配布する前にログのフィルター処理と集約を行うことができます。

|リソース|リンク|
|---|---|
|Log Analytics の Syslog データ ソース|[https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-syslog](../azure-monitor/agents/data-sources-syslog.md)|
|ログ収集サーバー|監視および SIEM のアーキテクチャの詳細については、ベンダーのドキュメントを参照してください|
|

### <a name="network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG)

NSG では、Azure 内の仮想ネットワークとの間で送受信されるトラフィックが制御されます。 NSG では、許可または拒否するトラフィック フローに対して規則を適用します。これには、Azure 内でのトラフィックと、Azure とオンプレミスやインターネットなどの外部ネットワークとの間のトラフィックが含まれます。 NSG は、仮想ネットワーク内のサブネットまたは個々のネットワーク インターフェイスに適用されます。 Azure のシステムを出入りするトラフィックに関する情報をキャプチャするには、Network Watcher NSG フロー ログ機能を使用して NSG ログを有効にすることができます。 これらのログは、システムの標準動作のベースラインを形成するために使用され、Traffic Analytics のデータ ソースになります。Traffic Analytics では、Azure でホストされているシステムのトラフィック パターンに関する詳細な分析情報が提供されます。

|リソース|リンク|
|---|---|
|ネットワーク セキュリティ グループのドキュメント|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|ネットワーク セキュリティ グループのフローのログ記録の概要|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview](../network-watcher/network-watcher-nsg-flow-logging-overview.md)|
|チュートリアル: Azure portal を使用して仮想マシンへの送受信ネットワーク トラフィックをログに記録する|[https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal](../network-watcher/network-watcher-nsg-flow-logging-portal.md)|
|

### <a name="azure-activity-log"></a>[Azure Activity Log (Azure アクティビティ ログ)]

Azure Monitor の一部である Azure アクティビティ ログは、Azure で発生したサブスクリプション レベルのイベントに関する分析情報を提供するサブスクリプション ログです。 アクティビティ ログを使用すると、サブスクリプションのリソース "***に対して***" 行われたすべての書き込み操作 (PUT、POST、DELETE) について、"何が、誰によって、いつ" 行われたのかを確認できます。 アクティビティ ログは、Azure 環境内で行われた構成の変更を追跡するために重要です。 Azure アクティビティ ログは、Log Analytics ソリューションでは自動的に使用でき、処理または保存のために Event Hubs または Azure Storage に送信できます。

|リソース|リンク|
|---|---|
|Azure アクティビティ ログのドキュメント|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|Event Hubs への Azure アクティビティ ログのストリーミング|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-stream-activity-logs-event-hubs](../azure-monitor/essentials/activity-log.md#legacy-collection-methods)|
|

### <a name="azure-diagnostic-log"></a>Azure 診断ログ

Azure Monitor 診断ログは、Azure のサービスによって生成されるログであり、そのサービスの操作に関する豊富なデータが頻繁に提供されます。 診断ログでは詳細レベルでのリソースの操作に関する分析情報が提供され、監査やトラブルシューティングなどのさまざまな要件に診断ログを使用できます。 Azure 診断ログは、Log Analytics ソリューションでは自動的に使用でき、処理または保存のために Event Hubs または Azure Storage に送信できます。

|リソース|リンク|
|---|---|
|Azure 診断ログのドキュメント|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs](../azure-monitor/essentials/platform-logs-overview.md)|
|診断ログのサポート サービス|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-diagnostic-logs-schema](../azure-monitor/essentials/resource-logs-schema.md)|
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy では、種類、場所、構成など、リソースのデプロイ方法に関する規則が適用されます。 Azure Policy は、要件に準拠している場合にのみリソースをデプロイできるように構成できます。 Azure Policy は、Azure 環境の整合性を維持するための中心的なコンポーネントです。 Azure Policy に関連するイベントは、Azure アクティビティ ログに記録され、Log Analytics ソリューションで自動的に使用でき、処理または保存のために Event Hubs または Azure Storage に送信できます。

|リソース|リンク|
|---|---|
|Azure Policy のドキュメント|[https://docs.microsoft.com/azure/governance/policy](../governance/policy/index.yml)|
|Azure Blueprints を使用した Azure Policy と Resource Manager テンプレートの利用|[https://docs.microsoft.com/azure/governance/blueprints/overview](../governance/blueprints/overview.md)|
|

## <a name="log-collection"></a>ログ コレクション

複数のログ ソースから生成されたログは、継続的なアクセスと分析のため、一箇所に保存する必要があります。 Azure ではログ収集に対して複数の方法とオプションが提供されており、ログの種類と要件に応じて利用できます。

### <a name="event-hubs"></a>Event Hubs

イベント ハブの目的は、配布のためにさまざまなソースのログ データを集約することです。 ログ データは、イベント ハブから SIEM、ACSC (準拠のため)、ストレージ (長期保存のため) に送信できます。

|リソース|リンク|
|---|---|
|Event Hubs のドキュメント|[https://docs.microsoft.com/azure/event-hubs](../event-hubs/index.yml)|
|Event Hubs と外部ツールに関するガイダンス|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-monitoring-data-event-hubs](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)|
|

### <a name="log-analytics"></a>Log Analytics

Log Analytics は Azure Monitor の一部であり、ログの分析に使用されます。 Log Analytics では、ストレージ メカニズムとしてワークスペースが使用され、Azure で利用できるさまざまな分析ツールやソリューションに対してログ データを使用できるます。 Log Analytics は、さまざまな Azure コンポーネントと直接統合され、Microsoft Monitoring Agent によって Virtual Machines と統合されます。

|リソース|リンク|
|---|---|
|Log Analytics のドキュメント|[https://docs.microsoft.com/azure/azure-monitor](../azure-monitor/index.yml)|
|チュートリアル: Log Analytics でデータを解析する|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-viewdata](../azure-monitor/logs/log-analytics-tutorial.md)|
|

### <a name="network-watcher"></a>Network Watcher

ACSC では、Azure サブスクリプションでのネットワーク トラフィックの把握とキャプチャを支援するため、Network Watcher の使用が推奨されます。 NSG フロー ログにより Log Analytics の Traffic Analytics ソリューションに入力が提供され、そこでは Azure を通じてネイティブにより高度な可視性、分析、レポートが提供されます。 Network Watcher では、また、Azure portal からパケット キャプチャ機能が直接提供され、仮想マシンにサインインする必要はありません。 パケット キャプチャを使用すると、仮想マシンとの間で送受信されるトラフィックを追跡するパケット キャプチャ セッションを作成できます。

|リソース|リンク|
|---|---|
|Network Watcher|[https://docs.microsoft.com/azure/network-watcher](../network-watcher/index.yml)|
|パケット キャプチャの概要|[https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview](../network-watcher/network-watcher-packet-capture-overview.md)|
|

## <a name="log-retention"></a>ログのリテンション期間

オーストラリア政府組織の場合、Azure 内でキャプチャされたログは National Archives of Australia [Administrative Functions Disposal Authority (AFDA)](https://www.naa.gov.au/information-management/records-authorities/types-records-authorities/afda-express-version-2-functions) に従って保持する必要があり、最大 7 年間ログを保持することが指定されています。

|ログの場所|リテンション期間|
|---|---|
|[Azure Activity Log (Azure アクティビティ ログ)]|最大 90 日|
|Log Analytics ワークスペース|最大 2 年|
|イベント ハブ|最大 7 日|
|

AFDA および他の法律上の要件に従ってログを適切にアーカイブすることは、お客様の責任です。

### <a name="azure-storage"></a>Azure Storage

Azure Storage は、Azure での長期的なログ保有のためのリポジトリです。 Azure Storage を使用すると、Event Hubs、Azure アクティビティログ、Azure 診断ログなど、Azure からのログをアーカイブできます。 Storage でのデータの保持期間は、0 に設定することも、日数で指定することもできます。 保持期間が 0 日の場合、ログは永続的に保持されます。それ以外の場合は、1 から 2147483647 の間の任意の日数を指定できます。

|リソース|リンク|
|---|---|
|Azure Storage のドキュメント|[https://docs.microsoft.com/azure/storage](../storage/index.yml)|
|Azure Event Hubs で Azure Blob Storage または Azure Data Lake Storage にイベントをキャプチャする|[https://docs.microsoft.com/azure/event-hubs/event-hubs-capture-overview](../event-hubs/event-hubs-capture-overview.md)|
|チュートリアル: Azure Storage を使用して Azure のメトリックとログのデータをアーカイブする|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-tutorial-archive-monitoring-data](../azure-monitor/essentials/platform-logs-overview.md)|
|Azure Storage レプリケーション|[https://docs.microsoft.com/azure/storage/common/storage-redundancy](../storage/common/storage-redundancy.md)|
|BLOB のスナップショットの作成|[https://docs.microsoft.com/rest/api/storageservices/creating-a-snapshot-of-a-blob](/rest/api/storageservices/creating-a-snapshot-of-a-blob)|
|

## <a name="log-analysis"></a>ログ分析

生成されて一箇所に保存されたログは、セキュリティ インシデントの試行または成功を検出するために分析する必要があります。 セキュリティ インシデントが検出されたとき、機関では、それらのインシデントに対応して、脅威を追跡、格納、および修復できる必要があります。

### <a name="microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud

Microsoft Defender for Cloud では、統合されたセキュリティ管理と高度な脅威に対する保護が提供されます。 Microsoft Defender for Cloud からは、ワークロード全体へのセキュリティ ポリシーの適用、脅威にさらされる状態の軽減、攻撃の検出とその対応を行うことができます。 Microsoft Defender for Cloud では、さまざまな Azure コンポーネントに対するダッシュボードと分析が提供されます。 Microsoft Defender for Cloud の使用は、ACSC コンシューマー ガイダンスの要件として指定されています。

|リソース|リンク|
|---|---|
|Microsoft Defender for Cloud のドキュメント|[https://docs.microsoft.com/azure/security-center](../security-center/index.yml)|
|クイックスタート: Microsoft Defender for Cloud の強化されたセキュリティ機能を有効にする|[https://docs.microsoft.com/azure/security-center/security-center-get-started](../security-center/enable-enhanced-security.md)|
|||

### <a name="traffic-analytics"></a>Traffic Analytics

Traffic Analytics は、Azure でのユーザーとアプリケーションのアクティビティを可視化するクラウドベースのソリューションです。 Traffic Analytics では、Network Watcher の NSG フロー ログが分析されて、Azure でのトラフィック フローに関する分析情報が提供されます。 Traffic Analytics は、仮想ネットワーク間で発生するネットワーク トラフィックに関連するダッシュボード、レポート、分析、およびイベント応答の機能を提供するために使用されます。 Traffic Analytics では、重要な分析情報が提供され、サイバー セキュリティ インシデントの特定と解決に役立ちます。

|リソース|リンク|
|---|---|
|Traffic Analytics のドキュメント|[https://docs.microsoft.com/azure/network-watcher/traffic-analytics](../network-watcher/traffic-analytics.md)|
|

### <a name="azure-advisor"></a>Azure Advisor

Azure Advisor では、リソースの構成と他のデータが分析されて、リソースのパフォーマンス、セキュリティ、高可用性を向上させ、Azure の全体的な使用量を削減する機会を探すのに役立つソリューションに関する推奨が提供されます。 Azure Advisor は ACSC によって推奨されており、Azure Advisor では Azure 環境の構成についての簡単にアクセスできる詳細なアドバイスが提供されます。

|リソース|リンク|
|---|---|
|Azure Advisor のドキュメント|[https://docs.microsoft.com/azure/advisor](../advisor/index.yml)|
|Azure Advisor の使用を開始する|[https://docs.microsoft.com/azure/advisor/advisor-get-started](../advisor/advisor-get-started.md)|
|

### <a name="dns-analytics-preview"></a>DNS Analytics (プレビュー)

DNS Analytics は、Windows DNS の分析ログと監査ログを収集して分析し、他の関連データと関連付ける、Log Analytics ソリューションです。 DNS Analytics では、悪意のあるドメイン名を解決しようとするクライアント、古いリソース レコード、頻繁にクエリされるドメイン名、および通信量の多い DNS クライアントが識別されます。 また DNS Analytics では、DNS サーバーでの要求の負荷および動的 DNS 登録エラーについての分析情報も提供されます。 DNS Analytics は、Azure 環境内で行われる DNS クエリに関連するダッシュボード、レポート、分析、およびイベント応答の機能を提供するために使用されます。 DNS Analytics では、重要な分析情報が提供され、サイバー セキュリティ インシデントの特定と解決に役立ちます。

|リソース|リンク|
|---|---|
|DNS Analytics のドキュメント|[https://docs.microsoft.com/azure/azure-monitor/insights/dns-analytics](../azure-monitor/insights/dns-analytics.md)|
|

### <a name="activity-log-analytics"></a>アクティビティ ログ分析

Activity Log Analytics は、複数の Azure サブスクリプションにわたる Azure アクティビティ ログの分析や検索に役立つ Log Analytics ソリューションです。 Activity Log Analytics は、Azure 環境全体のリソースに対して実行された操作に関連する一元的なダッシュボード、レポート、分析、およびイベント応答の機能を提供するために使用されます。 Activity Log Analytics は、監査と調査に役立ちます。

|リソース|リンク|
|---|---|
|Log Analytics での Azure アクティビティ ログの収集と分析|[https://docs.microsoft.com/azure/azure-monitor/platform/collect-activity-logs](../azure-monitor/essentials/activity-log.md)|
|

### <a name="security-information-and-event-management-siem"></a>セキュリティ情報イベント管理 (SIEM)

SIEM は、セキュリティ ログの集中的なストレージ、監査、分析を提供するシステムであり、分析、レポート、インシデント検出と応答のために、さまざまなログ データとインテリジェント ツールを取り込むためのメカニズムが定義されています。 Azure ログ情報を含む SIEM 機能を使用して、Azure でネイティブに提供されるセキュリティ機能を補完できます。 連邦機関では、特定の要件に応じて、Azure やオンプレミスの仮想マシンでホストされている SIEM、またはサービスとしてのソフトウェア (SaaS) 機能としての SIEM を利用できます。

|リソース|リンク|
|---|---|
|Microsoft Sentinel (プレビュー)|[https://azure.microsoft.com/services/azure-Sentinel](https://azure.microsoft.com/services/azure-sentinel)|
|SIEM のドキュメント|SIEM のアーキテクチャとガイダンスについては、ベンダーのドキュメントを参照してください|
|Azure Monitor を使用して SIEM ツールと統合する|[https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)|
|

### <a name="australian-cyber-security-centre"></a>Australian Cyber Security Centre

Australian Cyber Security Centre (ACSC) は、全国的なサイバー セキュリティに関するオーストラリア政府のリーダーです。 オーストラリア政府全体のサイバー セキュリティ機能を統合し、オーストラリアのコミュニティのサイバー復元性を向上させ、デジタル時代におけるオーストラリアの経済的および社会的な繁栄をサポートしています。 ACSC では、オーストラリア政府の監視のすべてについて、システムで生成されたすべての必須のログ ファイル、イベント、ログを ACSC に転送することが、連邦機関に対して推奨されています。

|リソース|リンク|
|---|---|
|Australian Cyber Security Centre の Web サイト|[https://www.acsc.gov.au](https://www.acsc.gov.au)|
|

## <a name="incident-response"></a>インシデント対応

適切なログを生成し、それらを集中リポジトリに収集して、分析を実行することで、システムについての理解が深まり、サイバー セキュリティ インシデントを検出するメカニズムが提供されます。 インシデントまたはイベントが検出された後の次のステップでは、それらのイベントに対応し、システムの正常性を維持するアクションを実行して、サービスとデータを侵害から保護します。 Azure には、発生したすべてのイベントに効果的に対応するためのサービスの組み合わせが用意されています。

### <a name="azure-alerts"></a>Azure アラート

Azure アラートを使用すると、特定のイベントに対応して、サポート担当者とセキュリティ担当者に通知することができます。 これにより、連邦機関は、この記事に記載されている分析サービスによって生成される関連イベントの検出に、事前に対応できます。

|リソース|リンク|
|---|---|
|Microsoft Azure のアラートの概要|[https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-alerts](../azure-monitor/alerts/alerts-overview.md)|
|Microsoft Defender for Cloud でのセキュリティ アラートの管理と対応|[https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts](../security-center/security-center-managing-and-responding-alerts.md)|
|Azure Monitor のログ アラート|[https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-response](../azure-monitor/alerts/alerts-log.md)|
|

### <a name="azure-automation"></a>Azure Automation

Azure Automation を使用すると、連邦機関はイベントに応答してアクションをトリガーできます。 これにより、仮想マシンでパケット キャプチャを開始したり、ワークフローを実行したり、仮想マシンやサービスを停止または開始したり、その他のさまざなタスクを実行したりすることができます。 Automation を使用すると、手動で介入しなくてもアラートに迅速に対応できるため、インシデントやイベントの応答時間を短縮したり重大度を軽減したりできます。

|リソース|リンク|
|---|---|
|Azure Automation のドキュメント|[https://docs.microsoft.com/azure/automation](../automation/index.yml)|
|攻略ガイド: アラートを使用して Azure Automation Runbook をトリガーする|[https://docs.microsoft.com/azure/automation/automation-create-alert-triggered-runbook](../automation/automation-create-alert-triggered-runbook.md)|
|

## <a name="next-steps"></a>次のステップ

Azure でゲートウェイ環境を安全に管理する方法の詳細について、[ゲートウェイの安全なリモート管理](gateway-secure-remote-administration.md)に関する記事をご覧ください。
