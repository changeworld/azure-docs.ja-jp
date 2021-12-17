---
title: Azure Australia でのエグレス トラフィックの制御
description: セキュリティで保護されたインターネット ゲートウェイに関するオーストラリア政府の要件を満たすための、Azure でのエグレス トラフィック制御の重要な要素
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: yvettep
ms.openlocfilehash: ded9ebca49183a8fd772fae440370cfd20677eee
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2021
ms.locfileid: "130265508"
---
# <a name="controlling-egress-traffic-in-azure-australia"></a>Azure Australia でのエグレス トラフィックの制御

ICT システムをセキュリティで保護するための基本コンポーネントは、ネットワーク トラフィックの制御です。 システムが機能するために必要なトラフィックのみに通信を制限することで、セキュリティ侵害の可能性を減らすことができます。 アプリケーションやサービスが通信する外部システムの可視性と制御によって、セキュリティ侵害を受けたシステムの検出と、試みられた、または成功したデータ流出の検出に役立ちます。 この記事では、Azure 内で送信 (エグレス) ネットワーク トラフィックがどのように機能するかについて説明します。また、Australian Cyber Security Centre (ACSC) Consumer Guidance および ACSC の Information Security Manual (ISM) の意図に沿った、インターネットに接続されたシステムのネットワーク セキュリティ制御を実装するための推奨事項について説明します。

## <a name="requirements"></a>要件

連邦政府システムの全体的なセキュリティ要件は ISM に規定されています。 連邦のエンティティによるネットワーク セキュリティの実装を支援するために、ACSC は _ACSC Protect: Implementing Network Segmentation and Segregation_ (ACSC 保護: ネットワークのセグメント化と分離の実装) を公開し、クラウド環境のシステムのセキュリティ保護を支援するために、ACSC は _テナントのクラウド コンピューティング セキュリティ_ を公開しました。

ACSC のドキュメントでは、ネットワーク セキュリティの実装とトラフィック制御のコンテキストを概説し、ネットワークの設計と構成に関する実用的な推奨事項を示しています。

Azure でのエグレス トラフィックの制御に関して、次の主な要件が ACSC のドキュメントに規定されています。

説明|source
--------------- |------------------
**ネットワークのセグメント化と分離を実装する**。たとえば、n 層アーキテクチャを採用し、ホストベースのファイアウォールとネットワーク アクセス制御を使用して、必要なポートとプロトコルのみに受信および送信ネットワーク接続を制限します。| [Cloud Computing for Tenants](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
テナントの可用性の要件を満たすために、テナント (テナントのリモート ユーザーを含む) とクラウド サービスの間に **適切な高帯域幅、低待機時間、信頼性の高いネットワーク接続を実装する**  | [ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**ネットワーク層だけに限らず技術を適用する**。 各ホストと各ネットワークは、可能な限り、実際に管理できる最下位レベルでセグメント化および分離する必要があります。 ほとんどの場合、これはデータ リンク層からアプリケーション レイヤーまでに適用されます。ただし、機密性の高い環境では、物理的な分離が適している場合があります。 ホストベースおよびネットワーク全体の対策を補完的な方法でデプロイし、一元的に監視する必要があります。 ファイアウォールまたはセキュリティ アプライアンスを唯一のセキュリティ対策として実装するだけでは不十分です。 |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**最小限の特権の付与と必要最小限の人にしか知らせない原則を使用する**。 あるホスト、サービス、またはネットワークと別のホスト、サービス、またはネットワークとの通信が必要ない場合、その通信を許可しないでください。 あるホスト、サービス、またはネットワークが、特定のポートまたはプロトコルで別のホスト、サービス、またはネットワークと通信する必要がある場合は、それらのポートとプロトコルのみに通信を制限する必要があります。 これらの原則をネットワーク全体にわたって導入すると、ユーザー特権の最小化を補完し、環境の全体的なセキュリティ体制を大幅に向上させることができます。 |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**業務運用上の機密性または重要度に基づいて、ホストおよびネットワークを分離する**。 これには、特定のホストまたはネットワークの、さまざまなセキュリティ分類、セキュリティ ドメイン、可用性や整合性に関する要件に応じて、さまざまなハードウェアまたはプラットフォームを使用することが含まれる場合があります。 特に、管理ネットワークを分離し、機密性の高い環境用に帯域外管理ネットワークを物理的に分離することを検討してください。 |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**すべてのエンティティによる他のすべてのエンティティへのアクセスを識別、認証、および承認する**。 すべてのユーザー、ホスト、サービスからその他すべてのユーザー、ホスト、サービスへのアクセスは、指定された職務または機能を実行するために必要なものだけに制限する必要があります。 識別、認証、および承認サービスの強度をバイパスまたはダウングレードするすべてのレガシ サービスまたはローカル サービスは、可能な限り無効にし、使用を厳重に監視する必要があります。 |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**ネットワーク トラフィックの拒否リストではなく許可リストを実装する**。 既知の無効なネットワーク トラフィックへのアクセスを拒否する (たとえば特定のアドレスやサービスをブロックする) のではなく、既知の有効なネットワーク トラフィック (識別、認証、および承認されたトラフィック) に対してのみアクセスを許可します。 許可リストは、結果的に拒否リストよりも優れたセキュリティ ポリシーになり、潜在的なネットワーク侵入を検出して評価する機能が大幅に向上します。 |[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装)
**許可されている Web サイトの許可リストを定義し、リストされていないすべての Web サイトをブロックする** と、敵対者が使用する最も一般的なデータ配信および流出手法の 1 つを排除できます。 多数の Web サイトや、急速に変化するリスト上の Web サイトにアクセスするための正当な必要性がユーザーにある場合は、このような実装のコストを考慮する必要があります。 比較的制限の少ない許可リストであっても、拒否リストに依存するよりも、または制限がまったくない場合よりもセキュリティが向上します。また、実装コストも削減できます。 制限の少ない許可リストの例として、オーストラリア サブドメイン ('*.au') 全体を許可することや、Alexa サイトのランク付けで上位 1,000 のサイトを (動的なドメイン ネーム システム (DDNS) ドメインやその他の不適切なドメインをフィルター処理した後で) 許可することが挙げられます。| [Australian Government Information Security Manual (ISM)](https://www.cyber.gov.au/ism)
|

この記事では、Azure 環境から出ていくネットワーク トラフィックの制御方法に関する情報と推奨事項について説明します。 サービスとしてのインフラストラクチャ (IaaS) を使用して Azure にデプロイされたシステムと、サービスとしてのプラットフォーム (PaaS) を使用して Azure にデプロイされたシステムの両方について説明します。

[ゲートウェイのイングレス トラフィック](gateway-ingress-traffic.md)に関する記事では、Azure 環境に入ってくるネットワーク トラフィックについて説明しています。また、これは完全なネットワーク制御を説明するための、この記事の補完となります。

## <a name="architecture"></a>Architecture

エグレス トラフィックを適切に制御するには、ネットワーク セキュリティを設計して実装するときに、まず、IaaS と PaaS の両方で Azure 内のエグレス ネットワーク トラフィックがどのように機能するかを理解する必要があります。 このセクションでは、Azure 内でホストされているリソースによって生成される送信トラフィックの処理方法と、そのトラフィックを制限および制御するために使用できるセキュリティ制御について概要を説明します。

### <a name="architecture-components"></a>アーキテクチャ コンポーネント

ここに示すアーキテクチャ図は、仮想ネットワーク内のサブネットにデプロイされたシステムから出ていくときに、ネットワーク トラフィックが通過する可能性があるパスを示しています。 仮想ネットワーク内のトラフィックは、中に含まれるリソースに対してルーティングおよびセキュリティ規則を適用して、サブネット レベルで管理および制御されます。 エグレス トラフィックに関連するコンポーネントは、システム、有効なルート、次ホップの種類、セキュリティ制御、および PaaS エグレスに分割されます。

![Architecture](media/egress-traffic.png)

### <a name="systems"></a>システム

システムは、仮想ネットワークの一部である IP サブネット内の送信トラフィックを生成する、Azure リソースおよび関連コンポーネントです。

| コンポーネント | 説明 |
| --- | ---|
|Virtual Network (VNet) | VNet は、リソースをデプロイして通信を可能にするためのプラットフォームと境界を提供する、Azure 内の基本リソースです。 VNet は Azure リージョン内に存在し、VNet に統合された仮想マシンなどのリソースの IP アドレス空間とルーティング境界を定義します。|
|Subnet | サブネットは、VNet 内に作成される IP アドレスの範囲です。 ネットワークのセグメント化のために、VNet 内に複数のサブネットを作成できます。|
|ネットワーク インターフェイス| ネットワーク インターフェイスは、Azure 内に存在するリソースです。 仮想マシンに接続され、関連するサブネットから、インターネットにルーティングできないプライベート IP アドレスが割り当てられます。 この IP アドレスは、Azure Resource Manager によって動的または静的に割り当てられます。|
|パブリック IP| パブリック IP は、仮想ネットワーク内で使用するために、指定したリージョンから、インターネットでルーティング可能な Microsoft 所有のパブリック IP アドレスの 1 つを予約するリソースです。 特定のネットワーク インターフェイスまたは PaaS リソースに関連付けることができます。これにより、リソースとインターネット、ExpressRoute、および他の PaaS システムとの通信が可能になります。|
|

### <a name="routes"></a>ルート

エグレス トラフィックが通過するパスは、そのリソースの有効なルートによって異なります。有効なルートは、可能なすべてのソースから取得されたルートの組み合わせと、Azure ルーティング ロジックの適用によって決定されるルートの結果セットです。

| コンポーネント | 説明 |
|--- | ---|
|システム ルート| Azure では、システム ルートが自動的に作成され、仮想ネットワークの各サブネットに割り当てられます。 システム ルートを作成または削除することはできませんが、一部はカスタム ルートでオーバーライドすることができます。 Azure では、サブネットごとに既定のシステム ルートが作成されます。また、Azure の特定の機能を使用するときは、特定のサブネットまたはすべてのサブネットにオプションの既定のルートが追加されます。|
|サービス エンドポイント| サービス エンドポイントにより、サブネットから特定の PaaS 機能への直接プライベート エグレス接続が提供されます。 サービス エンドポイントは、PaaS 機能のサブセットでのみ使用可能です。これを使用すると、PaaS にアクセスする VNet 内のリソースのパフォーマンスとセキュリティが向上します。|
|ルート テーブル| ルート テーブルは、ユーザー定義ルート (UDR) を指定するために作成できる、Azure 内のリソースです。これにより、システム ルートまたは Border Gateway Protocol によって取得されたルートを補完またはオーバーライドできます。 各 UDR で、ネットワーク、ネットワーク マスク、および次ホップを指定します。 ルート テーブルはサブネットに関連付けることができます。また、同じルート テーブルを複数のサブネットに関連付けることができますが、1 つのサブネットに使用できるルート テーブルは 0 個または 1 つだけです。|
|Border Gateway Protocol (BGP)| BGP は、自律システム間のルーティング プロトコルです。 自律システムは、共通の管理のもとで共通のルーティング ポリシーを使用するネットワークまたはネットワークのグループです。 BGP は、自律システム間でルーティング情報を交換するために使用されます。 BGP は、仮想ネットワーク ゲートウェイを介して仮想ネットワークに統合できます。|
|

### <a name="next-hop-types-defined"></a>次ホップの種類の定義

Azure 内の各ルートには、ネットワーク範囲および関連付けられたサブネット マスクと、トラフィックの処理方法を決定する次ホップが含まれています。

次ホップの種類 | 説明
---- | ----
**Virtual Network** | 仮想ネットワークのアドレス空間内のアドレス範囲間でトラフィックをルーティングします。 Azure によって、仮想ネットワークのアドレス空間に定義された各アドレス範囲に対応するアドレス プレフィックスを含むルートが作成されます。 仮想ネットワークのアドレス空間に複数のアドレス範囲が定義されている場合は、アドレス範囲ごとに個別のルートが作成されます。 アドレス範囲ごとに作成されたルートを使用して、vNet 内のサブネット間でトラフィックが自動的にルーティングされます。
**VNet ピアリング** | 2 つの仮想ネットワーク間に仮想ネットワーク ピアリングを作成すると、各仮想ネットワークのアドレス範囲ごとに、ピアリング先の仮想ネットワークにルートが追加されます。 トラフィックは、仮想ネットワーク内のサブネットと同じ方法で、ピアリングされた仮想ネットワーク間でルーティングされます。
**仮想ネットワーク ゲートウェイ** | 仮想ネットワークに仮想ネットワーク ゲートウェイを追加すると、次ホップの種類が仮想ネットワーク ゲートウェイである 1 つ以上のルートが追加されます。 ルートには、ローカル ネットワーク ゲートウェイのリソース内に構成されているルートと、BGP 経由で取得されたルートが含まれます。
**仮想アプライアンス** | 仮想アプライアンスでは、一般にネットワーク アプリケーション (ファイアウォールなど) が実行されています。 仮想アプライアンスを使用すると、フィルター処理、検査、アドレス変換など、トラフィックの追加処理を行うことができます。 ホップの種類が仮想アプライアンスである各ルートでは、次ホップの IP アドレスも指定します。
**VirtualNetworkServiceEndpoint** | サービス エンドポイントが有効になっている場合、特定のサービスのパブリック IP アドレスは、次ホップが VirtualNetworkServiceEndpoint であるサブネットへのルートとして追加されます。 サービス エンドポイントは、仮想ネットワーク内の個々のサブネット上の個々のサービスに対して有効になります。 Azure サービスのパブリック IP アドレスは定期的に変更されます。 アドレスが変更されると、Azure によってルート テーブルのアドレスが自動的に管理されます。
**Internet** | 次ホップがインターネットであるトラフィックは、仮想ネットワークから出て、関連付けられている Azure リージョン内で使用可能な動的プールから、またはそのリソースに対して構成されているパブリック IP アドレスを使用して、パブリック IP アドレスに自動的に変換されます。 宛先アドレスが Azure のサービスのいずれかのアドレスである場合、トラフィックはインターネットにルーティングされるのではなく、Azure のバックボーン ネットワーク経由でそのサービスに直接ルーティングされます。 仮想ネットワークが存在する Azure リージョン、または Azure サービスのインタンスがデプロイされている Azure リージョンに関係なく、Azure サービス間のトラフィックはインターネットを経由しません。
**なし** | 次ホップがなしのトラフィックは削除されます。 Azure では、予約済みアドレス プレフィクスの既定のシステム ルートは、次ホップの種類がなしで作成されます。 次ホップがなしのルートは、トラフィックが特定のネットワークにルーティングされるのを防ぐために、ルート テーブルを使用して追加することもできます。
|

### <a name="security-controls"></a>セキュリティ コントロール

コントロール | 説明
----- | -----
**ネットワーク セキュリティ グループ (NSG)** | Azure 内の仮想ネットワーク リソースとの間で送受信されるトラフィックを NSG によって制御します。 NSG では、許可または拒否するトラフィック フローに対して規則を適用します。これには、Azure 内でのトラフィックと、Azure とオンプレミスやインターネットなどの外部ネットワークとの間のトラフィックが含まれます。 NSG は、仮想ネットワーク内のサブネットまたは個々のネットワーク インターフェイスに適用されます。
**Azure Firewall** | Azure Firewall は、Azure Virtual Network リソースを保護する、クラウドベースのマネージド ネットワーク セキュリティ サービスです。 これは、組み込みの高可用性とクラウドによる無制限のスケーラビリティを備えた、完全にステートフルなサービスとしてのファイアウォールです。 Azure Firewall は、IP アドレス、プロトコル、およびポートに基づいた従来のネットワーク フィルタリング規則を使用して構成できますが、完全修飾ドメイン名 (FQDN)、サービス タグ、および組み込みの脅威インテリジェンスに基づくフィルター処理にも対応しています。
**ネットワーク仮想アプライアンス (NVA)** | ネットワーク仮想アプライアンスは、ネットワーク、セキュリティ、およびその他の機能を Azure に提供できる仮想マシン メディアです。 NVA により、仮想ネットワークおよびデプロイ内の VM という形でネットワーク機能やサービスがサポートされます。 NVA を使用して、特定の要件に対応したり、管理ツールや運用ツールと統合したり、既存の製品と一貫性を持たせたりすることができます。 Azure では、Web アプリケーション ファイアウォール (WAF)、ファイアウォール、ゲートウェイ/ルーター、アプリケーション配信コントローラー (ADC)、WAN オプティマイザーなど、幅広いサード パーティのネットワーク仮想アプライアンスをサポートしています。
**サービス エンドポイント ポリシー (プレビュー)** | 仮想ネットワーク サービス エンドポイント ポリシーでは、サービス エンドポイント経由での Azure サービスへの仮想ネットワーク トラフィックをフィルター処理し、特定の Azure サービス リソースのみを許可することができます。 エンドポイント ポリシーでは、Azure サービスへの仮想ネットワーク トラフィックに対する詳細なアクセス制御が提供されます。
**Azure Policy** | Azure Policy は、ポリシーの作成、割り当て、および管理を行うための Azure のサービスです。 これらのポリシーでは、規則を使用して、デプロイできるリソースの種類と、それらのリソースの構成を制御します。 ポリシーを使用すると、リソースが要件を満たしていない場合にリソースがデプロイされないようにすることで、コンプライアンスを強制できます。または、コンプライアンスの状態を報告するための監視にポリシーを使用できます。
|

### <a name="paas-egress"></a>PaaS エグレス

PaaS リソースの大部分からはエグレス トラフィックは生成されませんが、受信要求 (Application Gateway、Storage、SQL Database など) への応答、または他のリソース (Service Bus や Azure Relay など) からのデータの中継が行われます。 PaaS リソース間の (たとえば App Services から Storage または SQL Database への) ネットワーク通信フローは、Azure によって制御および包含され、ネットワークのセグメント化や分離ではなく、ID やその他のリソース構成制御によって保護されます。

仮想ネットワークにデプロイされた PaaS リソースは専用の IP アドレスを受け取り、仮想ネットワーク内の他のリソースと同じようにルーティング制御と NSG の影響を受けます。 仮想ネットワーク内に存在しない PaaS リソースは、リソースのすべてのインスタンス間で共有される IP アドレスのプールを利用します。IP アドレスは、Microsoft のドキュメントを通して公開されるか、Azure Resource Manager を通して確認できます。

## <a name="general-guidance"></a>一般的なガイダンス

セキュリティで保護されたソリューションを Azure 内で設計および構築するには、ネットワーク トラフィックを理解し、制御して、識別されて承認された通信だけを行うことができるようにすることが重要です。 このガイドと、後のセクションで説明する特定コンポーネントのガイダンスの意図は、[ACSC Protect: Implementing Network Segmentation and Segregation](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm) (ACSC 保護: ネットワークのセグメント化と分離の実装) で説明している原則を Azure ワークロード全体に適用するために利用できるツールとサービスについて説明することです。 これには、オンプレミス環境では可能な、従来と同じ物理的な制御およびネットワーク制御を適用できない場合に、リソースを保護するための仮想アーキテクチャを作成する方法の詳細が含まれます。

### <a name="guidance"></a>ガイダンス

* 仮想ネットワークのエグレス ポイントの数を制限する
* インターネットへの直接的な送信方向の通信を必要としないすべてのサブネットに対して、システムの既定のルートをオーバーライドする
* Azure リソースに対するすべてのイングレス ポイントおよびエグレス ポイントを識別し、制御するための、完全なネットワーク アーキテクチャを設計および実装する
* Microsoft 仮想データ センター (VDC) のドキュメントに説明されているように、仮想ネットワークにハブとスポークのネットワーク設計を利用することを検討する
* インターネットへの送信接続用に組み込みのセキュリティ機能を備えた製品を利用する (たとえば、Azure Firewall、ネットワーク仮想アプライアンス、Web プロキシ)
* Azure ロールベースのアクセス制御、条件付きアクセス、Multi-Factor Authentication (MFA) などの ID 制御を使用して、ネットワーク構成特権を制限する
* ネットワーク構成の重要な要素の変更または削除を防ぐためにロックを実装する
* 分離と制御を強化するために、VNet 統合構成で PaaS をデプロイする
* オンプレミス ネットワークとの接続のために ExpressRoute を実装する
* 外部ネットワークとの統合のために VPN を実装する
* Azure Policy を利用して、リージョンとリソースをシステムの機能に必要なもののみに制限する
* Azure Policy を利用して、リソースに対してベースライン セキュリティ構成を適用する
* Azure 内でのネットワーク トラフィックのログ記録、監査、可視性のために、Network Watcher と Azure Monitor を活用する

### <a name="resources"></a>リソース

Item | リンク
-----------| ---------
_オーストラリアの規制およびポリシー準拠に関するドキュメント (Consumer Guidance を含む)_ | [https://aka.ms/au-irap](https://aka.ms/au-irap)
_Azure 仮想データ センター_ | [https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](/azure/architecture/vdc/networking-virtual-datacenter)
_ACSC ネットワークのセグメント化_ | [https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)
_ACSC テナント向けのクラウド セキュリティ_ | [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)
_ACSC Information Security Manual_ | [https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)
|

## <a name="component-guidance"></a>コンポーネントのガイダンス

このセクションでは、Azure にデプロイされたシステムのエグレス トラフィックに関連する個々のコンポーネントに関する詳細なガイダンスを提供します。 各セクションでは、特定のコンポーネントの意図について説明し、設計と構築のアクティビティを支援するために使用できるドキュメントおよび構成ガイドへのリンクを示します。

### <a name="systems-security"></a>システムのセキュリティ

Azure 内のリソースへのすべての通信は、Microsoft によって管理されているネットワーク インフラストラクチャを通過します。このインフラストラクチャにより、接続とセキュリティ機能が提供されます。 Azure のプラットフォームとネットワーク インフラストラクチャを保護するために、Microsoft によってさまざまな保護が自動的に適用されます。また、ネットワーク トラフィックを制御し、ネットワークのセグメント化と分離を確立するために、Azure 内でサービスとして使用できる追加機能があります。

### <a name="virtual-network-vnet"></a>Virtual Network (VNet)

仮想ネットワークは、Azure におけるネットワークの基本的な構成要素の 1 つです。 仮想ネットワークでは、さまざまなシステムにまたがって使用される IP アドレス空間とルーティング境界を定義します。 仮想ネットワークはサブネットに分割され、仮想ネットワーク内のすべてのサブネットには相互に直接ネットワーク ルートがあります。 仮想ネットワーク ゲートウェイ (ExpressRoute または VPN) を使用することにより、仮想ネットワーク内のシステムをオンプレミス環境や外部環境と統合し、Azure によって提供されるネットワーク アドレス変換 (NAT) とパブリック IP アドレス割り当てを使用して、システムをインターネットまたはその他の Azure リージョンやサービスに接続できます。 仮想ネットワークと、関連する構成パラメーターおよびルーティングを理解することは、エグレス ネットワーク トラフィックを理解し、制御するうえで重要です。

Azure 内に仮想ネットワークのベース アドレス空間とルーティング境界が形成されるので、ネットワークのセグメント化と分離の主要な構成要素として Azure を使用できます。

| リソース | Link |
| --- | --- |
| *仮想ネットワークの概要* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](../virtual-network/virtual-networks-overview.md) |
| *仮想ネットワークの計画に関するハウツー ガイド*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md) |
| *仮想ネットワークの作成クイック スタート* | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](../virtual-network/quick-create-portal.md)
|

### <a name="subnet"></a>Subnet

サブネットは、Azure 内のネットワークのセグメント化と分離に不可欠なコンポーネントです。 サブネットを使用して、システム間の分離を実現できます。 サブネットは仮想ネットワーク内のリソースであり、NSG、ルート テーブル、およびサービス エンドポイントが適用されます。 サブネットは、ファイアウォール規則とアクセス制御リストの送信元と送信先の両方のアドレスとして使用できます。

仮想ネットワーク内のサブネットは、ワークロードとシステムの要件を満たすように計画する必要があります。 サブネットの設計または実装にかかわる個人は、ネットワークのセグメント化に関する ACSC のガイドラインを参照して、サブネット内でシステムをグループ化する方法を決定する必要があります。

|リソース|Link|
|---|---|
|*仮想ネットワーク サブネットの追加、変更、削除* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](../virtual-network/virtual-network-manage-subnet.md)
|

### <a name="network-interface"></a>ネットワーク インターフェイス

ネットワーク インターフェイスは、仮想マシンからのすべてのエグレス トラフィックのソースです。 ネットワーク インターフェイスによって IP アドレス指定の構成が可能になり、NSG を適用したり、NVA 経由でトラフィックをルーティングしたりするために使用できます。 仮想マシンのネットワーク インターフェイスは、ネットワークのセグメント化と分離の全体的な目標に合わせて計画し、適切に構成する必要があります。

|リソース|Link|
|---|---|
|*ネットワーク インターフェイスの作成、変更、または削除* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](../virtual-network/virtual-network-network-interface.md) |
|*ネットワーク インターフェイスの IP アドレス指定*               | [https://docs.microsoft.com/azure/virtual-network/private-ip-addresses](../virtual-network/ip-services/private-ip-addresses.md)
|

### <a name="vnet-integrated-paas"></a>VNet 統合 PaaS

PaaS によって、拡張された機能と可用性が提供され、管理のオーバーヘッドを削減できます。しかし、PaaS は適切に保護する必要があります。 制御を強化したり、ネットワークのセグメント化を適用したり、アプリケーションとサービスにセキュリティで保護されたエグレス ポイントを提供したりするために、多くの PaaS 機能を仮想ネットワークと統合できます。

PaaS をシステムまたはアプリケーション アーキテクチャの統合された部分として使用することで、PaaS を仮想ネットワークにデプロイするための複数のメカニズムが Microsoft によって提供されます。 デプロイ方法は、内部のシステムやアプリケーションとの接続および統合を実現しながら、アクセスを制限するのに役立ちます。 例として、App Service 環境、SQL Managed Instance などがあります。

ルーティングと NSG 制御が実装されている仮想ネットワークに PaaS をデプロイする場合は、Microsoft サービスからの管理アクセスと、それらのサービスからの受信要求に応答するときに通信トラフィックが通過するパスを含め、リソースの特定の通信要件を把握しておくことが重要です。

| リソース  | Link  |
| --- | --- |
| *Azure サービスの仮想ネットワーク統合* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](../virtual-network/virtual-network-for-azure-services.md) |
| *Azure 仮想ネットワークへのアプリの統合に関するハウツー ガイド* | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](../app-service/overview-vnet-integration.md)
|

### <a name="public-ip"></a>パブリック IP

パブリック IP アドレスは、仮想ネットワークの外部での通信に使用されます。 これには、PaaS リソースと、次ホップがインターネットであるルートが含まれます。 連邦政府機関は、パブリック IP アドレスの割り当てを慎重に計画し、正規の要件が存在するリソースにのみ割り当てる必要があります。 一般的な設計方法として、パブリック IP アドレスは、仮想ネットワークの制御されたエグレス ポイント (Azure Firewall、VPN Gateway、ネットワーク仮想アプライアンスなど) に割り当てる必要があります。

|リソース|Link|
|---|---|
|*パブリック IP アドレスの概要*  | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](../virtual-network/ip-services/public-ip-addresses.md#public-ip-addresses) |
|*パブリック IP アドレスの作成、変更、削除* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](../virtual-network/ip-services/virtual-network-public-ip-address.md)
|

## <a name="effective-routes"></a>有効なルート

有効なルートは、システム ルート、サービス エンドポイント、ルート テーブル、および BGP の組み合わせと、Azure ルーティング ロジックの適用によって決定されるルートの結果セットです。 送信トラフィックがサブネットから送信されると、Azure は最長プレフィックス一致アルゴリズムを使用して、宛先 IP アドレスに基づいてルートを選択します。 複数のルートに同じアドレス プレフィックスが含まれている場合は、次の優先順位に基づいてルートの種類が選択されます。

1. ユーザー定義のルート
2. BGP のルート
3. システム ルート

注意すべき重要なこととして、仮想ネットワーク、仮想ネットワークのピアリング、または仮想ネットワーク サービス エンドポイントに関連したトラフィックに使用されるシステム ルートは、BGP のルートの方が具体的であったとしても、優先されるルートとなります。

Azure でのルーティング トポロジの設計または実装にかかわる個人は、Azure によるトラフィックのルーティング方法を理解し、必要なシステム機能と、要求されるセキュリティおよび可視性とのバランスを取るアーキテクチャを開発する必要があります。 ルーティング動作への介入と例外が過剰に発生するのを防ぐために、環境を適切に計画するために注意が必要です。そうしないと、複雑さが増し、トラブルシューティングやエラー検出がより困難で時間のかかるものになります。

| リソース | 
| --- | 
| [有効なルートを表示する](../virtual-network/manage-route-table.md#view-effective-routes)

### <a name="system-routes"></a>システム ルート

[システム ルート](../virtual-network/virtual-networks-udr-overview.md#system-routes)について、仮想ネットワークの設計または実装にかかわる個人は、既定のシステム ルートと、それらのルートを補完またはオーバーライドするために使用できるオプションを理解している必要があります。

### <a name="service-endpoints"></a>サービス エンドポイント

サブネット上の[サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)を有効にすると、関連付けられている PaaS リソースへの直接通信パスが提供されます。 これにより、使用可能な通信パスをそのサービスのみに制限することで、パフォーマンスとセキュリティを向上させることができます。 既定の構成では、アプリケーションまたはシステムに必要な特定のインスタンスではなく、PaaS サービスのすべてのインスタンスへのアクセスが許可されるため、サービス エンドポイントを使用することによって、潜在的なデータ流出パスが導入されます。

連邦政府機関は、PaaS リソースへの直接アクセスの提供に関連するリスク (パスの誤用の可能性やその結果) を評価する必要があります。

サービス エンドポイントに関連する潜在的なリスクを軽減するには、可能であればサービス エンドポイント ポリシーを実装します。あるいは、Azure Firewall または NVA サブネットのサービス エンドポイントを有効にし、追加のフィルター処理、監視、検査を適用できるそのエンドポイントを介して、特定のサブネットからトラフィックをルーティングすることを検討します。

|リソース|Link|
|---|---|
|*チュートリアル: Azure Portal を使用して仮想ネットワーク サービス エンドポイントで PaaS リソースへのネットワーク アクセスを制限する* |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](../virtual-network/tutorial-restrict-network-access-to-resources.md)|
|

### <a name="route-tables"></a>ルート テーブル

ルート テーブルには、Azure 内のネットワーク トラフィックを制御するための、管理者によって構成されるメカニズムが用意されています。 ルート テーブルは、Azure Firewall または NVA へのトラフィックの転送、外部リソースへの直接接続、または Azure システム ルートのオーバーライドに使用できます。 また、ルート テーブルを使用すると、仮想ネットワーク ゲートウェイのルート伝達を無効にすることによって、仮想ネットワーク ゲートウェイを介して取得されたネットワークをサブネット内のリソースで使用できないようにすることもできます。

|リソース|Link|
|---|---|
|*ルーティングの概念 - カスタム ルート* |[https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes)|
|*チュートリアル: ネットワーク トラフィックのルーティング* |[https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal](../virtual-network/tutorial-create-route-table-portal.md)|
|

### <a name="border-gateway-protocol-bgp"></a>Border Gateway Protocol (BGP)

BGP は、オンプレミスまたは他の外部ネットワークとの間でルーティング情報を動的に交換するために、仮想ネットワーク ゲートウェイによって使用できます。 BGP は、ExpressRoute プライベート ピアリング経由の ExpressRoute 仮想ネットワーク ゲートウェイを使用して構成されている場合と、Azure VPN Gateway 上で有効になっている場合に、仮想ネットワークに適用されます。

Azure の仮想ネットワークと仮想ネットワーク ゲートウェイの設計または実装にかかわる個人は、Azure での BGP に使用できる動作と構成のオプションについて、時間を取って理解する必要があります。

|リソース|Link|
|---|---|
|*ルーティングの概念: BGP* | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#next-hop-types-across-azure-tools](../virtual-network/virtual-networks-udr-overview.md#next-hop-types-across-azure-tools)|
|*ExpressRoute のルーティングの要件* | [https://docs.microsoft.com/azure/expressroute/expressroute-routing](../expressroute/expressroute-routing.md)|
|*BGP と Azure VPN Gateway について* |[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview](../vpn-gateway/vpn-gateway-bgp-overview.md)|
|*チュートリアル: ExpressRoute Microsoft ピアリング経由のサイト間 VPN を構成する* |[https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering](../expressroute/site-to-site-vpn-over-microsoft-peering.md)|
|

## <a name="next-hop-types"></a>次ホップの種類

### <a name="virtual-network"></a>Virtual Network

次ホップが Virtual Network であるルートは、自動的にシステム ルートとして追加されますが、ユーザー定義のルートに追加して、システム ルートがオーバーライドされている場合にトラフィックを仮想ネットワークに転送し戻すこともできます。

### <a name="vnet-peering"></a>VNET ピアリング

VNet ピアリングにより、2 つの異なる仮想ネットワーク間の通信が可能になります。 各仮想ネットワークで VNet ピアリングを構成できるようになっている必要がありますが、仮想ネットワークは、同じリージョン、サブスクリプションに属する必要はなく、同じ Azure Active Directory (Azure AD) テナントに関連付けられている必要もありません。

VNet ピアリングを構成するとき、VNet ピアリングの設計または実装にかかわる個人が、関連する 4 つの構成パラメーターと、それらがどのようにピアの両側に適用されるかを理解することが重要です。

1. **[仮想ネットワーク アクセスを許可する]:** 2 つの仮想ネットワーク間の通信を有効にするには、 **[有効]** (既定値) を選択します。 仮想ネットワーク間の通信を有効にすると、各仮想ネットワークに接続されているリソースは、同じ仮想ネットワークに接続されている場合と同様に、同じ帯域幅と待機時間で相互に通信できます。
2. **[転送されたトラフィックを許可する]:** このボックスをオンにすると、ネットワークによって *転送された* トラフィック (仮想ネットワークから発生したものではないもの) を、ピアリングを通じてこの仮想ネットワークに流すことができます。 この設定は、ハブとスポークのネットワーク トポロジを実装するための基本となります。
3. **[ゲートウェイ転送を許可する]:** ピアリングされた仮想ネットワークが、この仮想ネットワークに接続された仮想ネットワーク ゲートウェイを使用することを許可する場合は、このボックスをオンにします。 "*ゲートウェイ転送を許可する*" は、仮想ネットワーク ゲートウェイ リソースを使用する仮想ネットワークで有効になりますが、他の仮想ネットワークで "*リモートゲートウェイを使用する*" が有効になっている場合にのみ適用されます。
4. **[リモート ゲートウェイを使用する]:** ピアリングされた仮想ネットワークに接続された仮想ネットワーク ゲートウェイ経由で、この仮想ネットワークからのトラフィックが流れることを許可する場合は、このボックスをオンにします。 "*リモート ゲートウェイを使用する*" は、仮想ネットワーク ゲートウェイがない仮想ネットワークで有効になりますが、他の仮想ネットワークで "*ゲートウェイの転送を許可する*" が有効になっている場合にのみ適用されます。

|リソース|Link|
|---|---|
| 概念: 仮想ネットワーク ピアリング                   | [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](../virtual-network/virtual-network-peering-overview.md) |
| 仮想ネットワーク ピアリングの作成、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](../virtual-network/virtual-network-manage-peering.md)|
|

### <a name="virtual-network-gateway"></a>仮想ネットワーク ゲートウェイ

仮想ネットワーク ゲートウェイにより、仮想ネットワークと外部ネットワーク (オンプレミス環境、パートナー環境、その他のクラウド デプロイなど) を統合するためのメカニズムが提供されます。 ExpressRoute と VPN という 2 種類の仮想ネットワーク ゲートウェイがあります。

#### <a name="expressroute-gateway"></a>ExpressRoute ゲートウェイ

ExpressRoute ゲートウェイは、仮想ネットワークからオンプレミス環境へのエグレス ポイントを提供するもので、セキュリティ、可用性、財務、およびパフォーマンスの要件を満たすようにデプロイする必要があります。 ExpressRoute ゲートウェイでは、定義されたネットワーク帯域幅が提供され、デプロイ後の使用コストが発生します。 仮想ネットワークに ExpressRoute ゲートウェイは 1 つだけ存在できますが、それを複数の ExpressRoute 回線に接続でき、VNet ピアリングを通じて複数の仮想ネットワークで利用できるため、帯域幅と接続を共有できます。 既知の制御されたネットワーク エグレス ポイントを使用してエンドツーエンドの接続を確保するために、ExpressRoute ゲートウェイを使用してオンプレミスの環境と仮想ネットワークの間のルーティングを構成するように注意が必要です。 ExpressRoute のプライベート ピアリング経由で ExpressRoute ゲートウェイを使用する連邦政府機関では、ACSC Consumer Guidance に準拠するために、ネットワーク仮想アプライアンス (NVA) をデプロイしてオンプレミス環境への VPN 接続を確立する必要もあります。

重要なこととして、ExpressRoute ゲートウェイでは、BGP を使用して交換されるアドレス範囲、コミュニティ、その他の特定の構成項目に制限があることに注意してください。

| リソース  | Link  |
|---|---|
| ExpressRoute ゲートウェイの概要 | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](../expressroute/expressroute-about-virtual-network-gateways.md) |
| ExpressRoute の仮想ネットワーク ゲートウェイを構成する | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
|

#### <a name="vpn-gateway"></a>VPN Gateway

Azure VPN Gateway により、セキュリティで保護されたサイト間接続のための、仮想ネットワークから外部ネットワークへのエグレス ネットワーク ポイントが提供されます。 VPN ゲートウェイには、定義されたネットワーク帯域幅が用意されており、デプロイ後に使用コストが発生します。 VPN ゲートウェイを使用している連邦政府機関では、それが ACSC Consumer Guidance に従って構成されていることを確認する必要があります。 仮想ネットワークに VPN ゲートウェイは 1 つだけ存在できますが、それを複数のトンネルを使用して構成でき、VNet ピアリングを通じて複数の仮想ネットワークで利用できます。これにより、複数の仮想ネットワークで帯域幅と接続を共有できます。 VPN ゲートウェイは、インターネット経由で、または Microsoft ピアリングを介して ExpressRoute 経由で確立できます。

| リソース  | Link |
| --- | --- |
| VPN ゲートウェイの概要| [https://docs.microsoft.com/azure/vpn-gateway](../vpn-gateway/index.yml)|
| VPN ゲートウェイの計画と設計 | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](../vpn-gateway/vpn-gateway-about-vpngateways.md)|
| Azure Australia の Azure VPN Gateway | [Azure Australia の Azure VPN Gateway](vpn-gateway.md)
|

### <a name="next-hop-of-virtual-appliance"></a>仮想アプライアンスの次ホップ

仮想アプライアンスの次ホップでは、仮想ネットワークに適用された Azure ネットワークとルーティング トポロジの外部のネットワーク トラフィックを処理することができます。 仮想アプライアンスを使用すると、セキュリティ規則を適用したり、アドレスを変換したり、VPN、プロキシ トラフィック、またはその他さまざまな機能を確立したりすることができます。 仮想アプライアンスの次ホップは、ルートテーブルの UDR を介して適用され、Azure Firewall、個々の NVA、または複数の NVA にわたって可用性を提供する Azure Load Balancer にトラフィックを転送することもできます。 仮想アプライアンスをルーティングに使用するには、関連付けられているネットワーク インターフェイスで IP 転送が有効になっている必要があります。

| リソース  | Link |
| --- | ---|
| ルーティングの概念: カスタム ルート | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes](../virtual-network/virtual-networks-udr-overview.md#custom-routes) |
| IP 転送の有効化と無効化 | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface#enable-or-disable-ip-forwarding](../virtual-network/virtual-network-network-interface.md#enable-or-disable-ip-forwarding)
|

### <a name="next-hop-of-virtualnetworkserviceendpoint"></a>VirtualNetworkServiceEndpoint の次ホップ

次ホップの種類が VirtualNetworkServiceEndpoint であるルートは、サブネットでサービス エンドポイントが構成されている場合にのみ追加され、ルート テーブルを使用して手動で構成することはできません。

### <a name="next-hop-of-internet"></a>インターネットの次ホップ

インターネットの次ホップは、パブリック IP アドレスを使用するすべてのリソースに接続するために使用されます。これには、インターネットだけでなく、Azure リージョンの PaaS および Azure サービスも含まれます。 インターネットの次ホップは、すべてのネットワークを対象とする既定のルート (0.0.0.0/0) を必要とせず、特定のパブリック サービスへのルーティング パスを有効にするために使用できます。 インターネットの次ホップを使用して、Microsoft 管理アドレスなど、システム機能に必要な承認されたサービスや機能へのルートを追加する必要があります。

インターネットの次ホップを使用して追加できるサービスの例を次に示します。

1. Windows のライセンス認証のためのキー管理サービス
2. App Service Environment 管理

|リソース|Link|
|---|---|
| Azure の Outbound connections | [https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections](../load-balancer/load-balancer-outbound-connections.md) |
| KMS ライセンス認証を有効にするために Azure カスタム ルートを使用する | [https://docs.microsoft.com/azure/virtual-machines/troubleshooting/custom-routes-enable-kms-activation](/troubleshoot/azure/virtual-machines/custom-routes-enable-kms-activation) |
| App Service Environment をロックする  | [https://docs.microsoft.com/azure/app-service/environment/firewall-integration](../app-service/environment/firewall-integration.md) |
|

### <a name="next-hop-of-none"></a>次ホップなし

次ホップなしは、特定のネットワークとの通信を防ぐために使用できます。 使用可能なネットワーク パスをトラフィックが通過することを許可するか拒否するかを制御する NSG とは対照的に、次ホップなしを使用すると、ネットワーク パスが完全に削除されます。 次ホップなしのルートを作成する場合、特にそれを既定のルートである 0.0.0.0/0 に適用する場合は注意が必要です。意図しない結果になる可能性があり、システムの問題についてのトラブルシューティングが、複雑で時間のかかるものになることがあるためです。

## <a name="security"></a>セキュリティ

IaaS および PaaS 機能にネットワークのセグメント化と分離の制御を実装する場合、それらの機能自体をセキュリティで保護し、それらの機能と通信を行うシステムからの制御された通信パスを実装することで、実装を実現できます。

Azure でのソリューションの設計と構築は、Azure の環境全体にわたってネットワーク リソースを把握、制御、監視するための論理アーキテクチャを作成するプロセスです。 この論理アーキテクチャは、Azure プラットフォーム内に定義されたソフトウェアであり、従来のネットワーク環境に実装されている物理ネットワーク トポロジに取って代わるものです。

作成される論理アーキテクチャは、使いやすくするために必要な機能を提供する必要がありますが、セキュリティと整合性に必要な可視性と制御も提供する必要があります。

この結果を実現する基礎になるのは、必要なネットワークのセグメント化と分離のツールを実装すること、また、ネットワーク トポロジとこれらのツールの実装を保護し、適用することです。

### <a name="network-security-groups-nsgs"></a>ネットワーク セキュリティ グループ (NSG)

NSG は、サブネットまたは特定のネットワーク インターフェイスに対して許可される受信トラフィックと送信トラフィックを指定するために使用されます。 NSG を構成する場合、連邦政府機関では承認リスト方法を使用する必要があります。この方法では、必要なトラフィックを許可するように規則を構成するとともに、特定の許可ステートメントに一致しないすべてのトラフィックを拒否する既定の規則を構成します。 NSG を計画および構成する際には、必要なすべての受信トラフィックと送信トラフィックが適切にキャプチャされるように注意する必要があります。 これには、仮想ネットワークとオンプレミス環境内で使用されているすべてのプライベート IP アドレス範囲と、特定の Microsoft サービス (Azure Load Balancer や PaaS 管理) の要件を識別し、把握することが含まれます。 NSG の設計と実装にかかわる個人は、サービスおよびアプリケーションに固有のきめ細かいセキュリティ規則を作成するための、サービス タグとアプリケーション セキュリティ グループの使用方法も理解している必要があります。

NSG の既定の構成では、仮想ネットワーク内のすべてのアドレスとすべてのパブリック IP アドレスへの送信トラフィックが許可されることに注意してください。

|リソース|Link|
|---|---|
|ネットワーク セキュリティの概要 | [https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|ネットワーク セキュリティ グループの作成、変更、削除 | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](../virtual-network/manage-network-security-group.md)|
|

### <a name="azure-firewall"></a>Azure Firewall

Azure Firewall は、ハブとスポークのネットワーク トポロジを構築し、集中管理されたネットワーク セキュリティ制御を適用するために使用できます。 Azure Firewall を使用すると、システム機能に必要な IP アドレス、プロトコル、ポート、および FQDN のみが承認される許可リストのアプローチを実装することによって、エグレス トラフィックに関して必要な ISM の要件を満たすことができます。 連邦政府機関では、Azure Firewall によって提供されるセキュリティ機能が要件に対して十分であるかどうかを判断するために、リスクベースのアプローチを取る必要があります。 Azure Firewall によって提供される機能以外の追加のセキュリティ機能が必要なシナリオでは、連邦政府機関では NVA を実装することを検討する必要があります。

|リソース|Link|
|---|---|
|*Azure Firewall のドキュメント* | [https://docs.microsoft.com/azure/firewall](../firewall/index.yml)|
|*チュートリアル: Azure PowerShell を使用してハイブリッド ネットワークに Azure Firewall をデプロイして構成する* | [https://docs.microsoft.com/azure/firewall/tutorial-hybrid-ps](../firewall/tutorial-hybrid-ps.md)|
|

### <a name="network-virtual-appliances-nvas"></a>ネットワーク仮想アプライアンス (NVA)

NVA を使用すると、ハブとスポークのネットワーク トポロジを構築し、拡張または補完的なネットワーク機能を提供できます。または、使い慣れたオンプレミスのネットワーク サービスでの一貫性のあるサポートと管理のために、Azure ネットワーク メカニズムの代替メカニズムとして NVA を使用できます。 NVA は、ネットワーク トラフィック、HTTPS 暗号化解除、コンテンツ検査、フィルター処理、またはその他のセキュリティ機能に関連する ID 認識の要件があるシナリオなど、特定のセキュリティ要件を満たすためにデプロイできます。 NVA は高可用性構成でデプロイする必要があります。また、NVA の設計または実装にかかわる個人は、Azure でのデプロイに関するガイドラインについて、適切なベンダーのドキュメントを参照する必要があります。

|リソース|Link|
|---|---|
|*高可用性のネットワーク仮想アプライアンスをデプロイする* | [https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha](/azure/architecture/reference-architectures/dmz/nva-ha)|
|

### <a name="service-endpoint-policies-preview"></a>サービス エンドポイント ポリシー (プレビュー)

サービス エンドポイント ポリシーは、サービスの可用性、およびデータ流出の確率と影響のセキュリティ リスク評価に基づいて構成します。 サービス エンドポイント ポリシーは、Azure Storage 用に検討し、関連付けられているリスク プロファイルに基づいて、他のサービス用にケースバイケースで管理する必要があります。

| リソース | Link  |
| --- | --- |
| *サービス エンドポイント ポリシーの概要* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview](../virtual-network/virtual-network-service-endpoint-policies-overview.md) |
| *Azure portal を使用してサービス エンドポイント ポリシーを作成、変更、または削除する* | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-portal](../virtual-network/virtual-network-service-endpoint-policies-portal.md)
|

### <a name="azure-policy"></a>Azure Policy

Azure Policy は、Azure 環境の論理アーキテクチャの整合性を適用し、維持するための重要なコンポーネントです。 Azure サービスを通じて利用できるさまざまなサービスとエグレス ネットワーク トラフィック パスがあります。 連邦政府機関では、使用している環境内に存在するリソースと、使用可能なネットワーク エグレス ポイントを認識していることが重要です。 Azure 環境内に未承認のネットワーク エグレス ポイントが作成されないようにするために、連邦政府機関では Azure Policy を使用して、デプロイできるリソースの種類とそれらのリソースの構成を制御する必要があります。 実際の例として、リソースを使用が許可されている承認済みのものだけに制限したり、NSG をサブネットに追加することを要求したりします。

|リソース | Link|
|---|---|
|*Azure Policy の概要* | [https://docs.microsoft.com/azure/governance/policy/overview](../governance/policy/overview.md)|
|*許可されるリソースの種類のサンプル ポリシー* | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](../governance/policy/samples/index.md)|
|*サブネットに NSG を強制するサンプル ポリシー*| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](../governance/policy/samples/index.md)|
|

## <a name="paas-egress-capabilities"></a>PaaS エグレス機能

PaaS の機能により、機能の向上と管理の簡素化の機会がもたらされますが、ネットワークのセグメント化と分離の要件に対応する際に複雑さが生じます。 PaaS 機能は、通常、パブリック IP アドレスを使用して構成され、インターネットからアクセスできます。  システムおよびソリューション内で PaaS 機能を使用している場合は、コンポーネント間の通信フローを特定し、その通信のみを許可するネットワーク セキュリティ規則を作成する必要があります。 セキュリティに対する多層防御アプローチの一環として、PaaS 機能は、暗号化、認証、および適切なアクセス制御とアクセス許可を使用して構成する必要があります。  

### <a name="public-ip-for-paas"></a>PaaS のパブリック IP

PaaS 機能のパブリック IP アドレスは、サービスがホストまたはデプロイされているリージョンに基づいて割り当てられます。 Azure 仮想ネットワーク、PaaS、ExpressRoute、インターネット接続を対象とするネットワークのセグメント化と分離のための適切なネットワーク セキュリティ規則とルーティング トポロジを構築する場合は、パブリック IP アドレスの割り当てとリージョンについて理解する必要があります。 Azure では、各 Azure リージョンに割り当てられたプールから IP アドレスが割り当てられます。 Microsoft では、各リージョンで使用されているアドレスをダウンロードできるようにしています。これは、制御された方法で定期的に更新されます。 各リージョンで利用可能なサービスは、新しいサービスがリリースされるか、サービスがより広範囲にデプロイされたときに頻繁に変化します。 連邦政府機関では、これらの資料を定期的に確認する必要があり、必要に応じて自動化を使ってシステムを維持することができます。 各リージョンでホストされている一部のサービスの特定の IP アドレスは、Microsoft サポートに連絡することによって取得できます。

| リソース | Link |
| --- | --- |
| *Microsoft Azure データセンターの IP 範囲*                   | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653) |
| *リージョン別の Azure サービス*                              | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all) |
| *Azure App Service における受信 IP アドレスと送信 IP アドレス* | [https://docs.microsoft.com/azure/app-service/overview-inbound-outbound-ips](../app-service/overview-inbound-outbound-ips.md)
|

## <a name="next-steps"></a>次のステップ

ご自身の全体的なアーキテクチャと設計を、公開されている [IaaS および PaaS Web アプリケーションの PROTECTED Blueprint](https://aka.ms/au-protected) に関するページを参照してください。