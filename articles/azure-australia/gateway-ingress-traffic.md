---
title: Azure オーストラリアでのイングレス トラフィックの制御
description: セキュリティで保護されたインターネット ゲートウェイに関するオーストラリア政府の要件を満たすために Azure オーストラリアのイングレス トラフィックを制御するためのガイド
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: bc1bef31da6082e33e14ba3e9bd5b3c8b480c130
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130253104"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Azure オーストラリアでのイングレス トラフィックの制御

ICT システムをセキュリティで保護するためのコア要素は、ネットワーク トラフィックの制御です。 セキュリティ侵害の可能性を減らすために、トラフィックは、システムが機能するために必要なもののみに制限する必要があります。

このガイドでは、Azure 内で受信 (イングレス) ネットワーク トラフィックがどのように機能するかについての詳細と、インターネットに接続されたシステムのネットワーク セキュリティ制御を実装するための推奨事項について説明します。

このネットワーク制御は、Australian Cyber Security Centre (ACSC) Consumer Guidance と ACSC の Information Security Manual (ISM) の意図に沿っています。

## <a name="requirements"></a>要件

連邦政府システムの全体的なセキュリティ要件は ISM に規定されています。 連邦のエンティティによるネットワーク セキュリティの実装を支援するために、ACSC は [ACSC Protect: Implementing Network Segmentation and Segregation](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装) を公開し、クラウド環境のシステムのセキュリティ保護を支援するために、ACSC は[テナントのクラウド コンピューティング セキュリティ](https://www.cyber.gov.au/publications/cloud-computing-security-for-tenants)を公開しました。

これらのガイドでは、ネットワーク セキュリティの実装とトラフィック制御のコンテキストを概説し、ネットワークの設計と構成に関する実用的な推奨事項を示しています。

Service Trust Portal のオーストラリアのページに記載されている [Microsoft Azure のテナント用の Microsoft クラウド コンピューティング セキュリティ](https://aka.ms/au-irap)に関するガイドでは、ACSC の刊行物にある助言を満たすための特定の Microsoft のテクノロジについて説明しています。

ACSC の刊行物で特定されている次の重要な要件は、Azure でイングレス トラフィックを制御するために重要です。

|説明|source|
|---|---|
|**ネットワークのセグメント化と分離を実装する。たとえば、ホストベースのファイアウォールと CSP のネットワーク アクセス制御を使用して、必要なポートとプロトコルのみに受信および送信 VM ネットワーク接続を制限する n 層アーキテクチャ。**| _Cloud Computing for Tenants_|
|テナントの可用性の要件を満たすために、テナント (テナントのリモート ユーザーを含む) とクラウド サービスの間に **適切な高帯域幅、低待機時間、信頼性の高いネットワーク接続を実装する**。  | _Cloud Computing for Tenants_|
|**ネットワーク層だけに限らず技術を適用する**。 各ホストと各ネットワークは、可能な限り、実際に管理できる最下位レベルでセグメント化および分離する必要があります。 ほとんどの場合、セグメント化および分離はデータ リンク層からアプリケーション レイヤーまでに適用されます。ただし、機密性の高い環境では、物理的な分離が適している場合があります。 ホストベースおよびネットワーク全体の対策を補完的な方法でデプロイし、一元的に監視する必要があります。 ファイアウォールまたはセキュリティ アプライアンスを唯一のセキュリティ対策として使用するだけでは不十分です。 |_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装)|
|**最小限の特権の付与と必要最小限の人にしか知らせない原則を使用する**。 あるホスト、サービス、またはネットワークと、別のホスト、サービス、またはネットワークとの通信が必要ない場合、それを許可しないでください。 あるホスト、サービス、またはネットワークが、特定のポートまたはプロトコルで別のホスト、サービス、またはネットワークと通信する必要がある場合は、他のすべてのポートあるいはプロトコルを無効にする必要があります。 これらの原則をネットワーク全体にわたって導入すると、ユーザー特権の最小化を補完し、環境の全体的なセキュリティ体制を大幅に向上させることができます。 |_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装)|
|**業務運用上の機密性または重要度に基づいて、ホストおよびネットワークを分離する**。 分離は、セキュリティ分類、セキュリティ ドメイン、可用性や整合性に関する特定のホストまたはネットワークの異なる要件に応じて、さまざまなハードウェアまたはプラットフォームを使用することで実現できます。 特に、管理ネットワークを分離し、機密性の高い環境用に帯域外管理ネットワークを物理的に分離することを検討してください。 |_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装)|
|**すべてのエンティティによる他のすべてのエンティティへのアクセスを識別、認証、および承認する**。 すべてのユーザー、ホスト、サービスからのアクセスは、指定された職務または機能を実行するために必要な別のユーザー、ホスト、サービスだけに制限する必要があります。 識別、認証、および承認サービスの強度をバイパスまたはダウングレードするレガシ サービスまたはローカル サービスはすべて無効にし、それらの使用を厳重に監視する必要があります。 |_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装)|
|**ネットワーク トラフィックの拒否リストではなく許可リストを実装する**。 既知の無効なネットワーク トラフィックへのアクセスを拒否する (たとえば特定のアドレスやサービスをブロックする) のではなく、既知の有効なネットワーク トラフィック (つまり、識別、認証、および承認されたトラフィック) に対してのみアクセスを許可します。 受け入れられた送信者一覧を使用することは、結果的にブロック リストよりも優れたセキュリティ ポリシーになり、潜在的なネットワーク侵入を検出して評価する組織の能力が大幅に向上します。 |_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装)|
|

この記事では、サービスとしてのインフラストラクチャ (IaaS) とサービスとしてのプラットフォーム (PaaS) の両方を使用して、Azure にデプロイされたシステムでこれらの要件を満たす方法に関する情報および推奨事項を提供します。 また、Azure 内でのネットワーク トラフィックの制御について完全に理解するには、「[Azure Australia でのエグレス トラフィックの制御](gateway-egress-traffic.md)」に関する記事も参照してください。

## <a name="architecture"></a>Architecture

ネットワーク セキュリティおよびイングレス トラフィックの制御の設計または実装に関与している場合、まず、IaaS と PaaS の両方で Azure 内のイングレス ネットワーク トラフィックがどのように機能するかを理解する必要があります。 このセクションでは、Azure 内でホストされているリソースにネットワーク トラフィックが到達する可能性があるエントリ ポイントと、そのトラフィックを制限および制御するために使用できるセキュリティ制御についての概要を示します。 これらの各コンポーネントについては、このガイドの残りのセクションで詳しく説明します。

### <a name="architecture-components"></a>アーキテクチャ コンポーネント

ここに示すアーキテクチャ図は、Azure でホストされているサービスに接続するためにネットワーク トラフィックが取る可能性があるパスを示しています。 これらのコンポーネントは、イングレス トラフィック用に提供する機能に応じて、Azure、IaaS イングレス、PaaS イングレス、およびセキュリティ制御に分割されます。

![Architecture](media/ingress-traffic.png)

### <a name="azure-components"></a>Azure コンポーネント

|コンポーネント | 説明|
|---|---|
|**DDoS Protection** | 分散型サービス拒否 (DDoS) 攻撃では、アプリケーションのリソースを使い果たし、正当なユーザーがアプリケーションを使用できなくなるようにすることが試みられます。 DDoS 攻撃は、インターネット経由で一般に到達可能なすべてのエンドポイントで実行できます。 Azure には、Azure プラットフォームを通じて自動的に DDoS Protection が組み込まれており、きめ細かい制御を実現するために特定のアプリケーションに対して有効にできる追加の緩和機能が提供されます。|
| **Traffic Manager** | Azure Traffic Manager は、Azure リージョン間でサービスへのトラフィックを最適に配分しつつ、高可用性と応答性を実現するドメイン ネーム システム (DNS) ベースのトラフィック ロード バランサーです。 Traffic Manager は、DNS を使用し、トラフィック ルーティング方法とエンドポイントの正常性に基づいて最適なエンドポイントにクライアント要求を誘導します。|
| **ExpressRoute** | ExpressRoute は、Microsoft クラウド サービスを利用するための専用のネットワーク接続です。 これは接続プロバイダーを通じてプロビジョニングされるため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。 ExpressRoute 回線は、接続プロバイダー経由のオンプレミス インフラストラクチャと Microsoft クラウド サービス間の論理接続を表します。|
| **ExpressRoute プライベート ピアリング** | ExpressRoute プライベート ピアリングは、オンプレミス環境とプライベート Azure 仮想ネットワークの間の接続です。 プライベート ピアリングにより、仮想ネットワーク内にデプロイされた仮想マシンなどの Azure サービスにアクセスできるようになります。 プライベート ピアリング経由でアクセスされるリソースと仮想ネットワークは、組織のコア ネットワークの拡張機能と見なされます。 プライベート ピアリングは、プライベート IP アドレスを使用してオンプレミス ネットワークと Azure 仮想ネットワークの間の双方向接続を提供します。|
| **ExpressRoute Microsoft ピアリング** | ExpressRoute Microsoft ピアリングは、オンプレミスの環境と Microsoft および Azure のパブリック サービスの間の接続です。 これには、Microsoft 365、Dynamics 365、および Azure PaaS サービスへの接続が含まれます。 ピアリングは、組織または接続プロバイダーが所有するパブリック IP アドレスを介して確立されます。 既定では、ExpressRoute Microsoft ピアリング経由でアクセスできるサービスはなく、組織は必要なサービスをオプトインする必要があります。 このプロセスによって、インターネット上で使用可能な同じエンドポイントに接続できるようになります。|
|

### <a name="iaas-ingress-components"></a>IaaS イングレスのコンポーネント

|コンポーネント | 説明|
|---|---|
|**ネットワーク インターフェイス** | ネットワーク インターフェイスは、Azure 内に存在するリソースです。 仮想マシンに接続され、関連するサブネットから、インターネットにルーティングできないプライベート IP アドレスが割り当てられます。 この IP アドレスは、Azure Resource Manager によって動的または静的に割り当てられます。|
|**サブネット** | サブネットは、VNet 内に作成される IP アドレスの範囲です。 ネットワークのセグメント化のために、VNet 内に複数のサブネットを作成できます。|
| **仮想ネットワーク (VNet)** | VNet は、リソースをデプロイして通信を可能にするためのプラットフォームと境界を提供する、Azure 内の基本リソースです。 VNet は Azure リージョン内に存在し、VNet に統合された仮想マシンなどのリソースの IP アドレス空間とルーティング境界を定義します。|
| **VNET ピアリング** | VNet ピアリングは、仮想ネットワーク ゲートウェイを必要とせずに 2 つの VNet 間の直接通信を可能にする Azure 構成オプションです。 ピアリングが完了すると、2 つの VNet は直接通信できるようになり、追加の構成によって仮想ネットワーク ゲートウェイやその他の転送オプションの使用を制御できます。|
| **パブリック IP** | パブリック IP は、仮想ネットワーク内で使用するために、指定したリージョンから、インターネットでルーティング可能な Microsoft 所有のパブリック IP アドレスの 1 つを予約するリソースです。 特定のネットワーク インターフェイスに関連付けることができます。これにより、インターネット、ExpressRoute、および PaaS システムからリソースへのアクセスが可能になります。|
| **ExpressRoute ゲートウェイ** | ExpressRoute ゲートウェイは、ExpressRoute 回線のプライベート ピアリング経由で仮想ネットワークからオンプレミス ネットワークへの接続とルーティングを提供する仮想ネットワーク内のオブジェクトです。|
| **VPN Gateway** | VPN Gateway は、仮想ネットワークから外部ネットワークへの暗号化されたトンネルを提供する仮想ネットワーク内のオブジェクトです。 暗号化されたトンネルは、オンプレミス環境、他の仮想ネットワーク、またはクラウド環境との双方向通信用のサイト間通信や、1 つのエンドポイントとの通信用のポイント対サイトにすることができます。|
| **PaaS VNet 統合** | 多くの PaaS 機能を仮想ネットワークにデプロイしたり、仮想ネットワークと統合したりすることができます。 一部の PaaS 機能は、VNet と完全に統合でき、プライベート IP アドレスのみを使用してアクセスできます。 その他の Azure Load Balancer や Azure Application Gateway などは、パブリック IP アドレスを使用する外部インターフェイスと、仮想ネットワーク内のプライベート IP アドレスを使用する内部インターフェイスを持つことができます。 この事例では、トラフィックは PaaS 機能を介して仮想ネットワークに入ることができます。|
|

### <a name="paas-ingress-components"></a>PaaS イングレスのコンポーネント

|コンポーネント | 説明|
|---|---|
|**hostname** | Azure PaaS の機能は、リソースの作成時に割り当てられる一意のパブリック ホスト名によって識別されます。 その後、このホスト名はパブリック DNS ドメインに登録され、パブリック IP アドレスに解決できるようになります。|
|**パブリック IP** | VNet 統合構成でデプロイされない限り、Azure PaaS の機能には、インターネットでルーティング可能なパブリック IP アドレスを使用してアクセスできます。 このアドレスは、パブリック ロード バランサーなどの特定のリソース専用にすることも、ストレージや SQL などの複数のインスタンスの共有エントリ ポイントを持つ特定の機能に関連付けることもできます。 このパブリック IP アドレスは、インターネット、ExpressRoute、または Azure バックボーン ネットワークを介した IaaS パブリック IP アドレスからアクセスできます。|
|**サービス エンドポイント** | サービス エンドポイントにより、仮想ネットワークから特定の PaaS 機能への直接プライベート接続が提供されます。 サービス エンドポイントは、PaaS 機能のサブセットでのみ使用可能です。これを使用すると、PaaS にアクセスする VNet 内のリソースのパフォーマンスとセキュリティが向上します。|
|

### <a name="security-controls"></a>セキュリティ コントロール

|コンポーネント | 説明|
|---|---|
|**ネットワーク セキュリティ グループ (NSG)** | Azure 内の仮想ネットワーク リソースとの間で送受信されるトラフィックを NSG によって制御します。 NSG では、許可または拒否するトラフィック フローに対して規則を適用します。これには、Azure 内でのトラフィックと、Azure とオンプレミスやインターネットなどの外部ネットワークとの間のトラフィックが含まれます。 NSG は、仮想ネットワーク内のサブネットまたは個々のネットワーク インターフェイスに適用されます。|
|**PaaS ファイアウォール** | ストレージや SQL など、多くの PaaS 機能には、特定のリソースへのイングレス ネットワーク トラフィックを制御するためのファイアウォールが組み込まれています。 特定の IP アドレスや仮想ネットワークからの接続を許可または拒否する規則を作成できます。|
|**PaaS 認証およびアクセス制御** | PaaS 機能はセキュリティに対する階層型アプローチの一部として、ユーザーを認証し、特権とアクセスを制御するための複数のメカニズムを提供します。|
|**Azure Policy** | Azure Policy は、ポリシーの作成、割り当て、および管理を行うための Azure のサービスです。 これらのポリシーでは、規則を使用して、デプロイできるリソースの種類と、それらのリソースの構成を制御します。 ポリシーを使用すると、リソースが要件を満たしていない場合にリソースがデプロイされないようにすることで、コンプライアンスを強制できます。または、コンプライアンスの状態を報告するための監視にポリシーを使用できます。|
|

## <a name="general-guidance"></a>一般的なガイダンス

セキュリティで保護されたソリューションを Azure 内で設計および構築するには、ネットワーク トラフィックを理解し、制御して、識別されて承認された通信だけを行うことができるようにすることが重要です。 このガイドと、後のセクションで説明する特定コンポーネントのガイダンスの意図は、_ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装) で説明している原則を Azure ワークロード全体に適用するために利用できるツールとサービスについて説明することです。 これには、オンプレミス環境では可能な、従来と同じ物理的な制御およびネットワーク制御を適用できない場合に、リソースを保護するための仮想アーキテクチャを作成する方法の詳細が含まれます。

### <a name="specific-focus-areas"></a>特定の対象領域

* 仮想ネットワークのエントリ ポイントの数を制限する
* パブリック IP アドレスの数を制限する
* Microsoft 仮想データ センター (VDC) のドキュメントに説明されているように、仮想ネットワークにハブとスポークのネットワーク設計を利用することを検討する
* インターネットからの受信接続用に組み込みのセキュリティ機能を備えた製品を利用する (たとえば、Application Gateway、API ゲートウェイ、ネットワーク仮想アプライアンス)
* PaaS 機能への通信フローを、システム機能に必要なもののみに制限する
* 分離と制御を強化するために、VNet 統合構成で PaaS をデプロイする
* ACSC Consumer Guidance および ISM に沿った暗号化メカニズムを使用するようにシステムを構成する
* 従来のネットワーク制御に加えて、認証や Azure ロールベースのアクセス制御などの ID ベースの保護を使用する
* オンプレミス ネットワークとの接続のために ExpressRoute を実装する
* 管理トラフィックおよび外部ネットワークとの統合のために VPN を実装する
* Azure Policy を利用して、リージョンとリソースをシステムの機能に必要なもののみに制限する
* Azure Policy を利用して、インターネットにアクセス可能なリソースに対してベースライン セキュリティ構成を適用する

### <a name="additional-resources"></a>その他のリソース

|リソース | Link|
|---|---|
|オーストラリアの規制およびポリシー準拠に関するドキュメント (Consumer Guidance を含む)|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Azure 仮想データ センター|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)|
|ACSC ネットワークのセグメント化|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ACSC テナント向けのクラウド セキュリティ| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|ACSC Information Security Manual|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>コンポーネントのガイダンス

このセクションでは、Azure にデプロイされたシステムへのイングレス トラフィックに関連する個々のコンポーネントに関する詳細なガイダンスを提供します。 各セクションでは、特定のコンポーネントの意図について説明し、設計と構築のアクティビティを支援するために使用できるドキュメントおよび構成ガイドへのリンクを示します。

## <a name="azure"></a>Azure

Azure 内のリソースへのすべての通信は、Microsoft によって管理されているネットワーク インフラストラクチャを通過します。このインフラストラクチャにより、接続とセキュリティ機能が提供されます。 Azure のプラットフォームとネットワーク インフラストラクチャを保護するために、Microsoft によってさまざまな保護が自動的に適用されます。また、ネットワーク トラフィックを制御し、ネットワークのセグメント化と分離を確立するために、Azure 内でサービスとして使用できる追加機能があります。

### <a name="ddos-protection"></a>DDoS Protection

インターネットからアクセスできるリソースは、DDoS 攻撃の影響を受けやすくなります。 これらの攻撃から保護するために、Azure では Basic レベルと Standard レベルの DDoS 保護を提供しています。

Basic は、Azure プラットフォームの一部として自動的に有効になります。これにはトラフィックの常時監視と一般的なネットワーク レベル攻撃のリアルタイム軽減が含まれ、Microsoft のオンライン サービスによって使用されるのと同じ防御が提供されます。  Azure のグローバル ネットワークのスケール全体を使用すると、各リージョンにまたがる攻撃トラフィックを分散および軽減できます。 IPv4 と IPv6 の Azure パブリック IP アドレスに対して保護が提供されます。

Standard は、Basic サービス レベルに加えて、特に Azure Virtual Network リソースに対してチューニングされた追加の軽減機能を提供します。 保護ポリシーは、専用のトラフィック監視および機械学習アルゴリズムによってチューニングされます。 IPv4 の Azure パブリック IP アドレスに対して保護が提供されます。

|リソース|Link|
|---|---|
|Azure DDoS Protection の概要|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](../ddos-protection/ddos-protection-overview.md)|
|Azure DDoS のベスト プラクティス|[https://docs.microsoft.com/azure/ddos-protection/fundamental-best-practices](../ddos-protection/fundamental-best-practices.md)|
|DDoS Protection の管理|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](../ddos-protection/manage-ddos-protection.md)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager は、アプリケーションのどのエンドポイントが接続を受信するかを制御することで、イングレス トラフィックを管理するために使用されます。 サイバー セキュリティ攻撃によるシステムまたはアプリケーションの可用性の損失を防ぐため、またはシステムの侵害後にサービスを復旧するために、Traffic Manager を使用して、機能している使用可能なアプリケーション インスタンスにトラフィックをリダイレクトすることができます。

|リソース|Link|
|---|---|
|Traffic Manager の概要 | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](../traffic-manager/traffic-manager-overview.md)|
|Azure DNS と Traffic Manager を使用したディザスター リカバリーのガイド | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](../networking/disaster-recovery-dns-traffic-manager.md)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute を使用して、オンプレミス環境から Azure でホストされているシステムへのプライベート パスを確立できます。 この接続により、ネットワーク通信のプライバシーが強化され、信頼性の向上と保証されたパフォーマンスが実現します。 ExpressRoute を使用すると、連邦政府機関はオンプレミス環境からの受信トラフィックを制御し、受信ファイアウォール規則とアクセス制御リストに使用する組織固有の専用のアドレスを定義できます。

|リソース | Link|
|---|---|
|ExpressRoute の概要 | [https://docs.microsoft.com/azure/expressroute/](../expressroute/index.yml)|
|ExpressRoute 接続モデル | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](../expressroute/expressroute-connectivity-models.md)|
|

### <a name="expressroute-private-peering"></a>ExpressRoute プライベート ピアリング

プライベート ピアリングは、プライベート IP アドレスのみを使用して、オンプレミス環境を Azure に拡張するためのメカニズムを提供します。 これにより、連邦政府機関は Azure Virtual Network およびアドレス範囲を既存のオンプレミス システムおよびサービスと統合できます。 プライベート ピアリングにより、ExpressRoute を介した通信が、組織によって承認された仮想ネットワークに対してのみ行われることが保証されます。 プライベート ピアリングを使用する場合、連邦政府機関は Azure VPN Gateway ではなくネットワーク仮想アプライアンス (NVA) を実装して、ACSC Consumer Guidance の要件に従って、オンプレミス ネットワークへのセキュリティで保護された VPN 通信を確立する必要があります。

|リソース | Link|
|---|---|
|ExpressRoute プライベート ピアリングの概要 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|ExpressRoute プライベート ピアリングに関するハウツー ガイド | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](../expressroute/expressroute-howto-routing-portal-resource-manager.md#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute Microsoft ピアリング

Microsoft ピアリングは、インターネットを経由せずに、Microsoft パブリック サービスに対する高速で待機時間の短い接続を提供します。 これにより、接続の信頼性、パフォーマンス、およびプライバシーが向上します。 ルート フィルターを使用すると、連邦政府機関は必要な Azure リージョンにのみ通信を制限できますが、これには他の組織がホストするサービスが含まれ、オンプレミス環境と Microsoft の間に追加のフィルタリングまたは検査機能が必要になる場合もあります。

連邦政府機関は、ピアリング関係を通じて確立された専用パブリック IP アドレスを使用して、PaaS 機能内のファイアウォールおよびアクセス制御リストで使用するオンプレミス環境を一意に識別できます。

別の方法として、連邦政府機関は、Azure VPN Gateway を介して VPN 接続を確立するために、ExpressRoute Microsoft ピアリングをアンダーレイ ネットワークとして使用することができます。 このモデルでは、内部のオンプレミス ネットワークから Azure パブリック サービスへの ExpressRoute 経由のアクティブな通信はありませんが、プライベート仮想ネットワークへのセキュリティで保護された接続が、ACSC Consumer Guidance に準拠して実現されます。

|リソース | Link|
|---|---|
|ExpressRoute Microsoft ピアリングの概要 | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](../expressroute/expressroute-circuit-peerings.md#routingdomains)|
|ExpressRoute Microsoft ピアリングに関するハウツー ガイド | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](../expressroute/expressroute-howto-routing-portal-resource-manager.md#msft)|
|

## <a name="iaas-ingress"></a>IaaS イングレス

このセクションでは、IaaS コンポーネントへのイングレス トラフィックを制御するためのコンポーネント ガイダンスを示します。 IaaS には、Azure の仮想ネットワーク内でデプロイおよび管理できる仮想マシンおよびその他のコンピューティング リソースが含まれています。 IaaS を使用してデプロイされたシステムにトラフィックが到達するには、仮想ネットワークへのエントリ ポイントが必要です。これはパブリック IP アドレス、仮想ネットワーク ゲートウェイ、または仮想ネットワーク ピアリング関係を介して確立できます。

### <a name="network-interface"></a>ネットワーク インターフェイス

ネットワーク インターフェイスは、仮想マシンへのすべてのトラフィックのイングレス ポイントです。 ネットワーク インターフェイスによって IP アドレス指定の構成が可能になり、NSG を適用したり、ネットワーク仮想アプライアンス経由でトラフィックをルーティングしたりするために使用できます。 仮想マシンのネットワーク インターフェイスは、ネットワークのセグメント化と分離の全体的な目標に合わせて計画し、適切に構成する必要があります。

|リソース | Link|
|---|---|
|ネットワーク インターフェイスの作成、変更、または削除 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md)|
|ネットワーク インターフェイスの IP アドレス指定 | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/ip-services/private-ip-addresses.md)|
|

### <a name="subnet"></a>Subnet

サブネットは、Azure 内のネットワークのセグメント化と分離に不可欠なコンポーネントです。 サブネットは、システム間の分離を提供するために同様に使用できます。 NSG をサブネットに適用して、イングレス通信フローを、システム機能に必要なもののみに制限することができます。 サブネットは、ファイアウォール規則とアクセス制御リストの送信元アドレスと宛先アドレスの両方として使用でき、PaaS 機能への接続を提供するようにサービス エンドポイントに対して構成できます。

|リソース | Link|
|---|---|
|仮想ネットワーク サブネットの追加、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)|
|

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

VNet は、Azure におけるネットワークの基本的な構成要素の 1 つです。 仮想ネットワークでは、さまざまなシステムにまたがって使用される IP アドレス空間とルーティング境界を定義します。 仮想ネットワークはサブネットに分割され、仮想ネットワーク内のすべてのサブネットには相互に直接ネットワーク ルートがあります。 仮想ネットワーク ゲートウェイ (ExpressRoute または VPN) を使用することにより、仮想ネットワーク内のシステムは、オンプレミス環境と外部環境からアクセスできるようになります。 仮想ネットワークと、関連する構成パラメーターおよびルーティングを理解することは、イングレス ネットワーク トラフィックを理解し、制御するうえで重要です。

|リソース | Link|
|---|---|
|仮想ネットワークの概要 | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md)|
|仮想ネットワークの計画に関するハウツー ガイド | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|
仮想ネットワークの作成クイック スタート | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)|
|

### <a name="vnet-peering"></a>VNet ピアリング

VNet ピアリングは、2 つの仮想ネットワーク間の直接通信パスを提供するために使用されます。 ピアリングが確立されると、1 つの仮想ネットワーク内のホストは、別の仮想ネットワーク内のホストへの直接の高速ルーティング パスを持ちます。 NSG は通常どおりトラフィックに適用されます。また、高度な構成パラメーターを使用して、仮想ネットワーク ゲートウェイを通じた通信または他の外部システムからの通信が許可されるかどうかを定義できます。

|リソース | Link|
|---|---|
|仮想ネットワーク ピアリングの概要 |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md)|
|仮想ネットワーク ピアリングの作成、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="public-ip-on-vnet"></a>VNET 上のパブリック IP

パブリック IP アドレスは、仮想ネットワークにデプロイされたサービスへのイングレス通信パスを提供するために使用されます。 連邦政府機関は、パブリック IP アドレスの割り当てを慎重に計画し、正規の要件が存在するリソースにのみ割り当てる必要があります。 一般的な設計上の方法として、パブリック IP アドレスを Application Gateway やネットワーク仮想アプライアンスなどの組み込みのセキュリティ機能を持つリソースに割り当てて、セキュリティで保護され、制御されたパブリック エントリ ポイントを仮想ネットワークに提供する必要があります。

|リソース | Link|
|---|---|
|パブリック IP アドレスの概要 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses)|
|パブリック IP アドレスの作成、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/ip-services/virtual-network-public-ip-address.md)|
|

### <a name="expressroute-gateway"></a>ExpressRoute ゲートウェイ

ExpressRoute ゲートウェイは、オンプレミス環境からのイングレス ポイントを提供するもので、セキュリティ、可用性、財務、およびパフォーマンスの要件を満たすようにデプロイする必要があります。 ExpressRoute ゲートウェイでは、定義されたネットワーク帯域幅が提供され、デプロイ後の使用コストが発生します。 仮想ネットワークに ExpressRoute ゲートウェイは 1 つだけ存在できますが、それを複数の ExpressRoute 回線に接続でき、VNet ピアリングを通じて複数の仮想ネットワークで利用できるため、複数の仮想ネットワークで帯域幅と接続を共有できます。 既知の制御されたネットワーク イングレス ポイントを使用してエンドツーエンドの接続を確保するために、ExpressRoute ゲートウェイを使用してオンプレミスの環境と仮想ネットワークの間のルーティングを構成するように注意が必要です。 ExpressRoute ゲートウェイを使用する連邦政府機関では、ACSC Consumer Guidance に準拠するために、ネットワーク仮想アプライアンスをデプロイしてオンプレミス環境への VPN 接続を確立する必要もあります。

|リソース | Link|
|---|---|
|ExpressRoute ゲートウェイの概要 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md)|
|ExpressRoute の仮想ネットワーク ゲートウェイを構成する | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway により、セキュリティで保護されたサイト間接続またはポイント対サイト接続のための、外部ネットワークからのイングレス ネットワーク ポイントが提供されます。 VPN ゲートウェイには、定義されたネットワーク帯域幅が用意されており、デプロイ後に使用コストが発生します。 VPN ゲートウェイを使用している連邦政府機関では、それが ACSC Consumer Guidance に従って構成されていることを確認する必要があります。 仮想ネットワークに VPN ゲートウェイは 1 つだけ存在できますが、それを複数のトンネルを使用して構成でき、VNet ピアリングを通じて複数の仮想ネットワークで利用できます。これにより、複数の仮想ネットワークで帯域幅と接続を共有できます。 VPN ゲートウェイは、インターネット経由で、または Microsoft ピアリングを介して ExpressRoute 経由で確立できます。

|リソース | Link|
|---|---|
|VPN ゲートウェイの概要 | [https://docs.microsoft.com/azure/vpn-gateway/](../vpn-gateway/index.yml)|
|VPN ゲートウェイの計画と設計 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
|オーストラリア政府機関向けの VPN ゲートウェイ構成|[オーストラリア政府機関に必要な IPSEC 構成](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>PaaS VNet 統合

PaaS を利用することで、拡張された機能と可用性が提供され、管理のオーバーヘッドを削減できますが、適切に保護する必要があります。 制御を強化したり、ネットワークのセグメント化を適用したり、アプリケーションとサービスにセキュリティで保護されたイングレス ポイントを提供したりするために、多くの PaaS 機能を仮想ネットワークと統合できます。

セキュリティで保護されたエントリ ポイントを提供するために、Application Gateway などの PaaS 機能は、外部の公開インターフェイス、およびアプリケーション サービスと通信するための内部のプライベート インターフェイスを使用して構成できます。 これにより、パブリック IP アドレスを使用してアプリケーション サーバーを構成して外部ネットワークに公開する必要がなくなります。

PaaS をシステムまたはアプリケーション アーキテクチャの統合された部分として使用するために、PaaS を仮想ネットワークにデプロイするための複数のメカニズムが Microsoft によって提供されます。 このデプロイ方法では、内部のシステムやアプリケーションとの接続および統合を実現しながら、インターネットなどの外部ネットワークからの受信アクセスが制限されます。 例として、App Service 環境、SQL マネージド インスタンスなどがあります。

|リソース | Link|
|---|---|
|Azure サービスの仮想ネットワーク統合 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md)|
|Azure 仮想ネットワークへのアプリの統合に関するハウツー ガイド | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/overview-vnet-integration.md)|
|

## <a name="paas-ingress"></a>PaaS イングレス

PaaS の機能により、機能の向上と管理の簡素化の機会がもたらされますが、ネットワークのセグメント化と分離の要件に対応する際に複雑さが生じます。 PaaS 機能は、通常、パブリック IP アドレスを使用して構成され、インターネットからアクセスできます。  PaaS 機能を使用してシステムを構築する場合は、システム内のコンポーネント間のすべての必要な通信フローと、この通信のみを許可するように作成されたネットワーク セキュリティ規則を特定するように注意が必要です。 セキュリティに対する多層防御アプローチの一環として、PaaS 機能は、暗号化、認証、および適切なアクセス制御とアクセス許可を使用して構成する必要があります。  

### <a name="hostname"></a>Hostname (ホスト名)

PaaS 機能は、同じサービスの複数のインスタンスを同じパブリック IP アドレスでホストできるようにするために、ホスト名によって一意に識別されます。 一意のホスト名は、リソースが作成され、Microsoft が所有する DNS ドメイン内に存在する場合に指定されます。 承認されたサービスの特定のホスト名は、アプリケーション レベルのフィルター処理機能を備えたセキュリティ ツール内で使用できます。 また、必要に応じてカスタム ドメインで特定のサービスを構成することもできます。

|リソース | Link|
|---|---|
|Azure サービスで使用される多くのパブリック名前空間は、Get-AzureRMEnvironment コマンドを実行して PowerShell を使用して取得できます | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](/powershell/module/azurerm.profile/get-azurermenvironment)|
|Azure クラウド サービスのカスタム ドメイン名の構成 | App Services などではカスタム ドメインを持つことができます [https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](../cloud-services/cloud-services-custom-domain-name-portal.md)|
|

### <a name="public-ip-for-paas"></a>PaaS のパブリック IP

PaaS 機能のパブリック IP アドレスは、サービスがホストまたはデプロイされているリージョンに基づいて割り当てられます。 Azure 仮想ネットワーク、PaaS、ExpressRoute、インターネット接続を対象とするネットワークのセグメント化と分離のための適切なネットワーク セキュリティ規則とルーティング トポロジを構築する場合は、パブリック IP アドレスの割り当てとリージョンについて理解する必要があります。 Azure では、各 Azure リージョンに割り当てられたプールから IP アドレスが割り当てられます。 Microsoft では、各リージョンで使用されているアドレスをダウンロードできるようにしています。これは、制御された方法で定期的に更新されます。 各リージョンで利用可能なサービスは、新しいサービスがリリースされるか、サービスがより広範囲にデプロイされたときに頻繁に変化します。 連邦政府機関では、これらの資料を定期的に確認する必要があり、必要に応じて自動化を活用してシステムを維持することができます。 各リージョンでホストされている一部のサービスの特定の IP アドレスは、Microsoft サポートに連絡することによって取得できます。

|リソース | Link|
|---|---|
|Microsoft Azure データセンターの IP 範囲 | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|リージョン別の Azure サービス | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>サービス エンドポイント

仮想ネットワーク サービスエンド ポイントは、特定の PaaS 機能を使用するために、仮想ネットワーク内のサブネットに対して高速なプライベート イングレス ネットワーク接続を提供します。 PaaS 機能の完全なネットワーク セグメント化と分離を実現するには、必要な仮想ネットワークからの接続のみを受け入れるように PaaS 機能を構成する必要があります。 サービスエンド ポイントを含むファイアウォール規則と従来の IP アドレスベースの規則の組み合わせがすべての PaaS 機能でサポートされるわけではないため、アプリケーションの機能と管理に必要な通信フローを理解し、これらのセキュリティ制御の実装がサービスの可用性に影響しないようにするように注意が必要です。

|リソース | Link|
|---|---|
|サービス エンドポイントの概要 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](../virtual-network/virtual-network-service-endpoints-overview.md)
|チュートリアル |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

## <a name="security"></a>セキュリティ

IaaS および PaaS 機能にネットワークのセグメント化と分離の制御を実装する場合、それらの機能自体をセキュリティで保護し、それらの機能と通信を行うシステムからの制御された通信パスを実装することで、実装を実現できます。

Azure でのソリューションの設計と構築は、Azure の環境全体にわたってネットワーク リソースを把握、制御、監視するための論理アーキテクチャを作成するプロセスです。 この論理アーキテクチャは、Azure プラットフォーム内に定義されたソフトウェアであり、従来のネットワーク環境に実装されている物理ネットワーク トポロジに取って代わるものです。

作成される論理アーキテクチャは、使いやすくするために必要な機能を提供する必要がありますが、セキュリティと整合性に必要な可視性と制御も提供する必要があります。

この結果を実現する基礎になるのは、必要なネットワークのセグメント化と分離のツールを実装すること、また、ネットワーク トポロジとこれらのツールの実装を保護し、適用することです。

このガイドで提供されている情報は、許可する必要があるイングレス トラフィックのソースと、トラフィックをさらに制御または制限する方法を特定するために使用できます。

### <a name="network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG)

NSG は、サブネットまたは特定のネットワーク インターフェイスに対して許可される受信トラフィックと送信トラフィックを指定するために使用されます。 NSG を構成する場合、連邦政府機関では承認リスト方法を使用する必要があります。この方法では、必要なトラフィックを許可するように規則を構成します。既定の規則は、特定の許可ステートメントに一致しないすべてのトラフィックを拒否するように構成されています。 NSG を計画および構成する際には、必要なすべての受信トラフィックと送信トラフィックが適切に取り込まれるように注意する必要があります。 これには、Azure Virtual Networks とオンプレミス環境内で使用されているすべてのプライベート IP アドレス範囲と、特定の Microsoft サービス (Azure Load Balancer や PaaS 管理) の要件を識別し、把握することが含まれます。 ネットワーク セキュリティ グループの設計と実装にかかわる個人は、サービスおよびアプリケーションに固有のきめ細かいセキュリティ規則を作成するための、サービス タグとアプリケーション セキュリティ グループの使用方法も理解している必要があります。

|リソース | Link|
|---|---|
|ネットワーク セキュリティの概要 | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)
|ネットワーク セキュリティ グループの作成、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

## <a name="paas-firewall"></a>PaaS ファイアウォール

PaaS ファイアウォールは、特定の PaaS サービスに適用できるネットワーク アクセス制御機能です。 IP アドレス フィルター処理や特定の仮想ネットワークからのフィルター処理を、特定の PaaS インスタンスへのイングレス トラフィックを制限するように構成することができます。 ファイアウォールを含む PaaS 機能については、アプリケーション要件に基づいて必要なイングレス トラフィックのみを許可するようにネットワーク アクセス制御ポリシーを構成する必要があります。  

|リソース | Link|
|---|---|
|Azure SQL Database と Azure Synapse Analytics の IP ファイアウォール規則 | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](../azure-sql/database/firewall-configure.md)|
|ストレージ ネットワーク セキュリティ | [https://docs.microsoft.com/azure/storage/common/storage-network-security](../storage/common/storage-network-security.md)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS 認証およびアクセス制御

PaaS の機能とその目的によっては、ネットワーク制御を使用したアクセスの制限は、不可能であったり、実用的ではなかったりする場合があります。 PaaS の多層型セキュリティ モデルの一部として、Azure には、ネットワーク トラフィックが許可されている場合でも、サービスへのアクセスを制限するためのさまざまな認証およびアクセス制御のメカニズムが用意されています。 PaaS 機能の一般的な認証メカニズムには、Azure Active Directory、アプリケーション レベルの認証、共有キーまたはアクセス署名などがあります。 ユーザーが安全に識別されたら、ロールを使用して、ユーザーが実行できるアクションを制御することができます。 これらのツールは、サービスへのアクセスを制限するための代替手段または補完手段として使用できます。

|リソース | Link|
|---|---|
|SQL Database と Azure Synapse Analytics へのデータベース アクセスの制御と許可 | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](../azure-sql/database/logins-create-manage.md)|
|Azure Storage サービスの承認 | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy は、Azure 環境の論理アーキテクチャの整合性を適用し、維持するための重要なコンポーネントです。 Azure サービスを介して利用できるさまざまなサービスとイングレス ネットワーク トラフィック パスがあるため、連邦政府機関では、環境内に存在するリソースと使用可能なネットワーク イングレス ポイントを認識することが重要です。 Azure 環境内に未承認のネットワーク イングレス ポイントが作成されないようにするために、連邦政府機関では Azure Policy を活用して、デプロイできるリソースの種類とそれらのリソースの構成を制御する必要があります。 実際の例として、リソースを、使用が許可されている承認済みのものだけに制限したり、ストレージの HTTPS 暗号を強制したり、NSG をサブネットに追加することを要求したりします。

|リソース | Link|
|---|---|
|Azure Policy の概要 | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|許可されるリソースの種類のサンプル ポリシー | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)
|HTTPS ストレージ アカウントの確認のサンプル ポリシー|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](../governance/policy/samples/index.md)_
|サブネットに NSG を強制するサンプル ポリシー| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="next-steps"></a>次のステップ

[ゲートウェイのエグレス トラフィックの管理と制御](gateway-egress-traffic.md)に関する記事で、Azure におけるゲートウェイ コンポーネントを使用した Azure 環境から別のネットワークへのトラフィック フロー管理の詳細を確認してください。