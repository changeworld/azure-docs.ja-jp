---
title: SAP HANA on Azure (L インスタンス) のネットワーク アーキテクチャ | Microsoft Docs
description: SAP HANA on Azure (Large Instances) をデプロイするためのネットワーク アーキテクチャについて説明します。
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/21/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6e37ab692b1e8f4a498d4b279ef3e45940e7d7f2
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2021
ms.locfileid: "128708322"
---
# <a name="sap-hana-large-instances-network-architecture"></a>SAP HANA (L インスタンス) のネットワーク アーキテクチャ

この記事では、SAP HANA on Azure Large Instances (BareMetal インフラストラクチャとも呼ばれます) をデプロイするためのネットワーク アーキテクチャについて説明します。 

Azure ネットワーク サービスのアーキテクチャは、HANA Large Instance への SAP アプリケーションのデプロイを成功させるための重要な要素です。 通常、SAP HANA on Azure (Large Instances) のデプロイには、大規模な SAP ランドスケープがあります。 これらには、さまざまなサイズのデータベース、CPU リソースの消費量、メモリ使用量を含むいくつかの SAP ソリューションが含まれると考えられます。 

すべての IT システムが既に Azure に配置されているとは限りません。 また、お客様の SAP ランドスケープはハイブリッドである場合もあります。 データベース管理システム (DBMS) と SAP アプリケーションでは、NetWeaver、S/4HANA、および SAP HANA を組み合わせて使用できます。 SAP アプリケーションでは、別の DBMS を使用することもできます。

Azure では、DBMS、NetWeaver、および S/4HANA の各システムを Azure で運用できるようにする、さまざまなサービスを提供しています。 Azure には、オンプレミスのソフトウェア デプロイで Azure を仮想データ センターのように取り扱うためのネットワーク テクノロジが用意されています。 Azure ネットワーク機能には、次のものが含まれます。 

- オンプレミスのネットワーク資産に接続する [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 回線に接続された Azure 仮想ネットワーク。
- [1 Gbps 以上](https://azure.microsoft.com/pricing/details/expressroute/)の帯域幅を備えた、オンプレミスを Azure に接続する ExpressRoute 回線。 この回線により、オンプレミスのシステムと仮想マシン (VM) で実行されているシステム間のデータ転送用の十分な帯域幅を提供できます。 また、オンプレミスのユーザーから Azure システムへの接続用の十分な帯域幅も確保できます。
- 相互に通信できるよう仮想ネットワークでセットアップされている、Azure 内のすべての SAP システム。
- オンプレミスでホストされる Active Directory と DNS は、ExpressRoute 経由でオンプレミスから Azure に拡張されます。 完全に Azure 内で実行することもできます。

HANA Large Instances を Azure データ センター ネットワーク ファブリックに統合するときは、Azure ExpressRoute テクノロジも使用されます。


> [!NOTE] 
> 特定の Azure リージョンに存在する HANA L インスタンス スタンプ内の 1 つのテナントには、Azure サブスクリプションを 1 つだけリンクできます。 逆に、1 つの Azure サブスクリプションには、HANA L インスタンス スタンプ内のテナントを 1 つだけリンクできます。 この要件は、Azure の他の課金対象オブジェクトと変わりません。

SAP HANA on Azure (Large Instances) を複数の Azure リージョンにデプロイすると、Large Instances スタンプに別のテナントがデプロイされます。 これらのインスタンスが同じ SAP ランドスケープに含まれていれば、それらを同じ Azure サブスクリプションで実行できます。 

> [!IMPORTANT] 
> SAP HANA on Azure (L インスタンス) でサポートされるのは、Azure Resource Manager を使ったデプロイ方法だけです。

## <a name="extra-virtual-network-information"></a>仮想ネットワークの追加情報

仮想ネットワークを ExpressRoute に接続するには、Azure ExpressRoute ゲートウェイを作成する必要があります。 詳細については、[ExpressRoute 用の ExpressRoute ゲートウェイについて](../../../expressroute/expressroute-about-virtual-network-gateways.md)のページを参照してください。 

Azure ExpressRoute ゲートウェイは、Azure 外部のインフラストラクチャまたは Azure L インスタンス スタンプへの ExpressRoute で使用されます。 Azure ExpressRoute ゲートウェイは最大で 4 つの ExpressRoute 回線に接続できますが、これは、それらの接続元がそれぞれ異なる Microsoft Enterprise Edge ルーター (MSEE) である場合に限られます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md)」をご覧ください。 

> [!NOTE] 
> ExpressRoute 接続を使用して ExpressRoute ゲートウェイで実現できる最大スループットは 10 Gbps です。 仮想ネットワーク内の VM とオンプレミスのシステム間で (単一のコピー ストリームとして) ファイルをコピーすると、さまざまなゲートウェイ SKU の最大限のスループットを実現できません。 Azure ExpressRoute ゲートウェイのすべての帯域幅を活用するには、複数のストリームを使用するか、1 つのファイルのパラレル ストリームで複数のファイルをコピーする必要があります。


## <a name="networking-architecture-for-hana-large-instance"></a>HANA L インスタンスのネットワーク アーキテクチャ
HANA Large Instances のネットワーク アーキテクチャは、次の 4 つの部分に分けることができます。

- オンプレミス ネットワークと Azure への ExpressRoute 接続。 この部分はお客様の領域であり、ExpressRoute 経由で Azure に接続されます。 この ExpressRoute 回線に関する支払いは、全額がお客様の負担となります。 帯域幅は、オンプレミスの資産と接続先の Azure リージョンの間のネットワーク トラフィックを処理するのに十分な大きさにする必要があります。 次の図の右下の部分を参照してください。
- 仮想ネットワークを使用した前述の Azure ネットワーク サービス。繰り返しになりますが、仮想ネットワークには ExpressRoute ゲートウェイを追加する必要があります。 この部分では、アプリケーション、セキュリティ、コンプライアンスの要件を満たすために、適切な設計を作成する必要があります。 選択する仮想ネットワークと Azure ゲートウェイ SKU の数を考慮して、HANA Large Instances を使用するかどうかを検討してください。 図の右上の部分を参照してください。
- Azure ExpressRoute を使用した Azure への HANA Large Instances の接続。 この部分は Microsoft によってデプロイされ、処理されます。 お客様が行う必要があるのは、HANA Large Instances への資産のデプロイ後に、Azure ExpressRoute 回線を仮想ネットワークに接続する IP アドレス範囲を指定するだけです。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md)」をご覧ください。 Azure データ センター ネットワーク ファブリックと HANA Large Instances ユニットの間の接続に関する追加料金はありません。
- HANA L インスタンス スタンプ内のネットワーキング。ほとんどの場合、お客様の側でこれを意識することはありません。

![SAP HANA on Azure (L インスタンス) およびオンプレミスに接続された仮想ネットワーク](./media/hana-overview-architecture/image1-architecture.png)

HANA Large Instances を使用している場合でも、次の 2 つの要件は引き続き保持されます。
- オンプレミスの資産は、ExpressRoute 経由で Azure に接続する必要があります。
- VM を実行する 1 つ以上の仮想ネットワークが必要です。 これらの VM は、HANA Large Instances でホストされている HANA インスタンスに接続するアプリケーション層をホストします。

Azure での SAP デプロイの違いは次のとおりです。

- テナントの HANA Large Instances は、別の Azure ExpressRoute 回線を介して仮想ネットワークに接続されます。 オンプレミスと Azure 仮想ネットワークの間の Azure ExpressRoute 回線と、Azure 仮想ネットワークと HANA Large Instances の間の回線は、同じルーターを共有しません。 それらの負荷状態は分離されたままになります。
- SAP アプリケーション層と HANA Large Instances 間のワークロード プロファイルは、異なる特性を示します。 SAP HANA は、アプリケーション層への大量の小さな要求とデータ転送 (結果セット) などのバーストを生成します。
- SAP アプリケーション アーキテクチャは、オンプレミスと Azure 間でデータが交換される標準的なシナリオよりも、ネットワーク待機時間の影響を受けやすくなります。
- Azure ExpressRoute ゲートウェイには、少なくとも 2 つの ExpressRoute 接続があります。 1 つはオンプレミスを接続元とする回線であり、1 つは HANA Large Instances を接続元とする回線です。 この構成では、異なる MSEE からの 2 つの回線が ExpressRoute ゲートウェイに接続する余地だけが残ります。 この制限は、ExpressRoute FastPath の使用とは無関係です。 接続されているすべての回線は、ExpressRoute ゲートウェイの受信データの最大帯域幅を共有します。

HANA Large Instances スタンプのリビジョン 3 では、VM と HANA Large Instances ユニット間で発生するネットワーク待機時間は、VM 間のネットワーク ラウンド トリップの標準的な待機時間よりも長くなる可能性があります。 Azure リージョンによっては、値は「[SAP Note #1100926 - FAQ: Network performance (SAP ノート #1100926 - FAQ: ネットワーク パフォーマンス)](https://launchpad.support.sap.com/#/notes/1100926/E)」で平均以下に分類されているラウンド トリップの待機時間 (0.7 ms) を超える可能性があります。 Azure VM と HANA Large Instances ユニットの間のネットワーク ラウンド トリップ待機時間を測定するツールと Azure リージョンによっては、待機時間が最大 2 ミリ秒になる可能性があります。 それでも、お客様は SAP HANA ベースの実稼働 SAP アプリケーションを SAP HANA Large Instances に問題なくデプロイしています。 Azure HANA Large Instances でビジネス プロセスを十分にテストしてください。 ExpressRoute FastPath と呼ばれる新しい機能では、Azure の HANA Large Instances とアプリケーション レイヤー VM の間のネットワーク待ち時間を大幅に削減できます (下記参照)。 

HANA Large Instances スタンプのリビジョン 4 では、HANA Large Instances スタンプに近接してデプロイされる Azure VM 間のネットワーク待ち時間が改善されます。 待ち時間は、Azure ExpressRoute FastPath が構成されている場合、「[SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E)」に記載されている平均または平均の分類を満たす、またはそれ以上になります (下記参照)。 

リビジョン 4 の HANA Large Instances に近接して Azure VM をデプロイするには、[Azure 近接通信配置グループ](../../co-location.md)を適用する必要があります。 近接配置グループは、リビジョン 4 ホステッド HANA Large Instances と同じ Azure データセンター内に SAP アプリケーション レイヤーを配置するために使用できます。 詳細については、「[SAP アプリケーションで最適なネットワーク待ち時間を実現する Azure 近接通信配置グループ](sap-proximity-placement-scenarios.md)」を参照してください。

VM と HANA Large Instances の間に決定論的なネットワーク待ち時間を実現するには、Azure ExpressRoute ゲートウェイ SKU の使用が不可欠となります。 オンプレミスと VM 間のトラフィック パターンとは異なり、VM と HANA Large Instances 間のトラフィック パターンでは、要求やデータ量は小さくても、大きなバーストが発生する可能性があります。 このようなバーストを適切に処理するために、UltraPerformance ゲートウェイ SKU を使用することを強くお勧めします。 Type II クラスの HANA Large Instances SKU の場合、ExpressRoute ゲートウェイとして UltraPerformance ゲートウェイ SKU を使用することが必須となります。

> [!IMPORTANT] 
> SAP アプリケーション層とデータベース層の間の全体的なネットワーク トラフィックを考慮し、SAP HANA on Azure (L インスタンス) への接続では、仮想ネットワーク用の HighPerformance または UltraPerformance ゲートウェイ SKU だけがサポートされます。 HANA L インスタンスの Type II SKU の場合、ExpressRoute ゲートウェイとしてサポートされるのは UltraPerformance ゲートウェイ SKU だけです。 ExpressRoute FastPath を使用する場合は、例外が適用されます (下記参照)。

### <a name="expressroute-fastpath"></a>ExpressRoute FastPath
2019 年 5 月に、ExpressRoute FastPath がリリースされました。 FastPath によって、HANA Large Instances と SAP アプリケーション VM をホストする Azure 仮想ネットワーク間の待機時間が短縮されます。 FastPath を使用することで、VM と HANA Large Instances の間のデータ フローは、Azure ExpressRoute ゲートウェイを通じてルーティングされなくなります。 Azure 仮想ネットワークのサブネットに割り当てられている VM が、専用のエンタープライズ エッジ ルーターと直接通信しています。 

> [!IMPORTANT] 
> Azure ExpressRoute FastPath では、SAP アプリケーション VM が稼働しているサブネットが、HANA Large Instances に接続されているのと同じ Azure 仮想ネットワーク内にあることが必要です。 HANA Large Instances ユニットに接続されている Azure 仮想ネットワークにピアリングされている Azure 仮想ネットワーク内の VM には、Azure ExpressRoute FastPath のメリットはありません。 その結果、ExpressRoute 回線がハブ仮想ネットワークに接続し、SAP アプリケーション レイヤーを含む仮想ネットワーク (スポーク) がピアリングされている典型的なハブ アンド スポーク仮想ネットワーク設計では、ExpressRoute FastPath による最適化は機能しません。 ExpressRoute FastPath は、現在、ユーザー定義ルーティング規則 (UDR) をサポートしていません。 詳細については、「[ExpressRoute 仮想ネットワーク ゲートウェイと FastPath](../../../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください。 


Azure ExpressRoute FastPath を構成する方法の詳細については、「[HANA Large Instances に仮想ネットワークを接続する](./hana-connect-vnet-express-route.md)」を参照してください。    

> [!NOTE]
> ExpressRoute FastPath を使用するには、UltraPerformance ExpressRoute ゲートウェイが必要です。


## <a name="single-sap-system"></a>単一の SAP システム

上記のオンプレミスのインフラストラクチャは ExpressRoute 経由で Azure に接続されます。 ExpressRoute 回線は、MSEE に接続されます。 詳細については、[ExpressRoute の技術概要](../../../expressroute/expressroute-introduction.md)に関する記事をご覧ください。 ルートが確立されると、そのルートは Azure バックボーンに接続されます。

> [!NOTE] 
> Azure で SAP ランドスケープを実行するには、SAP ランドスケープの Azure リージョンに最も近いエンタープライズ エッジ ルーターに接続します。 Azure IaaS 内の VM と HANA Large Instances スタンプの間のネットワーク待ち時間を最小限に抑えるために、HANA L インスタンス スタンプは専用のエンタープライズ エッジ ルーターを介して接続されます。

SAP アプリケーション インスタンスをホストする VM の ExpressRoute ゲートウェイは、オンプレミスに接続する 1 つの ExpressRoute 回線に接続されます。 同じ仮想ネットワークが、独立したエンタープライズ エッジ ルーターに接続されます。 このエッジ ルーターは、Large Instance スタンプへの接続専用です。 ここでも、FastPath を使用することで、HANA Large Instances から SAP アプリケーション レイヤー VM へのデータ フローは、Azure ExpressRoute ゲートウェイを通じてルーティングされることはありません。 この構成により、ネットワーク ラウンドトリップの待機時間が短縮されます。

このシステムは、単一の SAP システムのわかりやすい例です。 SAP アプリケーション層は Azure でホストされます。 SAP HANA データベースは SAP HANA on Azure (L インスタンス) で実行されます。 ExpressRoute ゲートウェイの帯域幅 (2 Gbps または 10 Gbps のスループット) がボトルネックにならないことが前提となります。

## <a name="multiple-sap-systems-or-large-sap-systems"></a>複数の SAP システムまたは大規模な SAP システム

SAP HANA (Large Instances) に接続するために複数の SAP システムまたは大規模な SAP システムをデプロイする場合、Azure ExpressRoute ゲートウェイのスループットがボトルネックになることがあります。 このような場合は、アプリケーション層を複数の仮想ネットワークに分割します。 異なる Azure 仮想ネットワークで運用システムと非運用システムを分離する場合は、アプリケーション レイヤーを分割することもできます。 

次の場合には、HANA Large Instances に接続する特別な仮想ネットワークを作成できます。

- HANA Large Instances 内の HANA インスタンスから、NFS 共有をホストする Azure の VM に直接バックアップを実行する。
- HANA Large Instances から、バックアップ ファイルなど大きなファイルを Azure 内の管理されたディスク領域にコピーする。

HANA L インスタンスと Azure の間で大量のデータを転送するためのストレージを管理する VM をホストするには、別の仮想ネットワークを使用します。 この配置では、HANA Large Instances から Azure に大きなファイルやデータを転送することによって、SAP アプリケーション レイヤーを運用する VM にサービスを提供する ExpressRoute ゲートウェイに及ぶことはありません。 

拡張可能なネットワーク アーキテクチャでは次のように対応します。

- 1 つの大規模な SAP アプリケーション層に対して、複数の仮想ネットワークを使用します。
- 同じ仮想ネットワークの個々のサブネットに SAP システムの組み合わせを配置するのではなく、デプロイされた SAP システムごとに個別の仮想ネットワークをデプロイします。

    次の図は、SAP HANA on Azure (Large Instances) の拡張可能なネットワーク アーキテクチャを示しています。

![複数の仮想ネットワークにわたる SAP アプリケーション層のデプロイ](./media/hana-overview-architecture/image4-networking-architecture.png)

さまざまな SAP システムの VM をホストしているさまざまな仮想ネットワークの間で適用する規則や制限によっては、それらの仮想ネットワークをピアリングする必要があります。 仮想ネットワーク ピアリングの詳細については、「[仮想ネットワーク ピアリング](../../../virtual-network/virtual-network-peering-overview.md)」をご覧ください。


## <a name="routing-in-azure"></a>Azure でのルーティング

既定のデプロイでは、SAP HANA on Azure (L インスタンス) に関して、ネットワーク ルーティングについての 3 つの考慮事項が重要です。

- SAP HANA on Azure (L インスタンス) には、Azure VM および専用の ExpressRoute 接続経由でのみアクセスできます。オンプレミスから直接アクセスすることはできません。 オンプレミスから HANA L インスタンス ユニットへの直接アクセスは、Microsoft から提供されるため、すぐには実現できません。 この一時的なルーティングの制限は、SAP HANA Large Instances に使用されている現在の Azure ネットワーク アーキテクチャに起因します。 直接アクセスを必要とする管理クライアントやアプリケーション (オンプレミスで実行されている SAP Solution Manager など) は、SAP HANA データベースに接続できません。 例外については、「[HANA L インスタンスへの直接ルーティング](#direct-routing-to-hana-large-instances)」のセクションを確認してください。

- ディザスター リカバリーのために 2 つの異なる Azure リージョンにデプロイされている HANA Large Instances ユニットがある場合、以前は同じ一時的なルーティングの制限が適用されました。 つまり、一方のリージョン (例: 米国西部) の HANA Large Instances の IP アドレスが、もう一方のリージョン (例: 米国東部) でデプロイされている HANA Large Instances にルーティングされることはありませんでした。 この制限は、リージョン間の Azure ネットワーク ピアリング、または HANA Large Instances を仮想ネットワークに接続する ExpressRoute 回線の交差接続の使用とは無関係です。 図については、「[複数のリージョンでの HANA L インスタンス ユニットの使用](#use-hana-large-instance-units-in-multiple-regions)」の図を参照してください。 デプロイ済みのアーキテクチャにはこのような制限があったため、HANA システム レプリケーションをディザスター リカバリーのためにすぐに使用することはできませんでした。 最近の変更については、「[複数のリージョンでの HANA L インスタンス ユニットの使用](#use-hana-large-instance-units-in-multiple-regions)」セクションを再度参照してください。 

- SAP HANA on Azure Large Instances には、お客様が HANA Large Instances のデプロイを要求したときに提出したサーバー IP プールのアドレス範囲から IP アドレスが割り当てられます。 詳細については、「[Azure での SAP HANA on Azure (L インスタンス) のインフラストラクチャと接続](hana-overview-infrastructure-connectivity.md)」をご覧ください。 この IP アドレスには、Azure サブスクリプションと、Azure 仮想ネットワークを HANA L インスタンスに接続する回線を使用してアクセスできます。 サーバー IP プールのアドレス範囲から割り当てられる IP アドレスは、ハードウェア ユニットに直接割り当てられます。 このソリューションの最初のデプロイでこの割り当てが行われるため、ネットワーク アドレス変換 (NAT) を使用した割り当ては *行われません*。 

### <a name="direct-routing-to-hana-large-instances"></a>HANA L インスタンスへの直接ルーティング

既定では、推移的なルーティングは次のシナリオでは機能しません。

- HANA Large Instances ユニットとオンプレミスのデプロイの間。

- 異なるリージョンにデプロイされている HANA Large Instances ユニットの間。

それらのシナリオで推移的なルーティングを有効にするには 3 つの方法があります。

- データをルーティングするリバース プロキシ。 たとえば、仮想ファイアウォールおよびトラフィック ルーティング ソリューションとして HANA L インスタンスやオンプレミスに接続する Azure 仮想ネットワークにデプロイされる F5 BIG-IP や NGINX (および Traffic Manager) などです。
- Linux VM で IPTables ルールを使用して、オンプレミスの場所と HANA L インスタンス ユニット間、または別のリージョンにある HANA L インスタンス ユニット間のルーティングを有効にします。 IPTables が実行される VM は、HANA Large Instances およびオンプレミスに接続している Azure 仮想ネットワークにデプロイする必要があります。 VM は、VM のネットワーク スループットが予想ネットワーク トラフィックに対して十分であるように、サイズを設定する必要があります。 VM のネットワーク帯域幅の詳細については、「[Azure の Linux 仮想マシンのサイズ](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)」という記事を参照してください。
- [Azure Firewall](https://azure.microsoft.com/services/azure-firewall/) は、オンプレミスと HANA L インスタンス ユニットの間の直接のトラフィックを可能にするもう 1 つのソリューションです。 

これらのソリューションのすべてのトラフィックは、Azure 仮想ネットワーク経由でルーティングされます。 そのため、トラフィックは、使用されるソフト アプライアンスまたは Azure ネットワーク セキュリティ グループによって制限される可能性もあります。 この方法で、オンプレミスの特定の IP アドレスまたは IP アドレス範囲は、HANA Large Instances へのアクセスをブロックする、または明示的に許可できます。 

> [!NOTE]  
> サード パーティのネットワーク アプライアンスまたは IPTables を使用するカスタム ソリューションの実装とサポートは、Microsoft では提供されていないことに注意してください。 使用するコンポーネントのベンダーまたはインテグレーターがサポートを提供する必要があります。 

#### <a name="express-route-global-reach"></a>Express Route Global Reach
Microsoft は、[ExpressRoute Global Reach](../../../expressroute/expressroute-global-reach.md) という新しい機能を導入しました。 Global Reach は、2 つのシナリオで HANA L インスタンスに使用できます。

- 異なるリージョンにデプロイされている HANA Large Instances ユニットへのオンプレミスからの直接アクセスを可能にします。
- 異なるリージョンにデプロイされている HANA Large Instances ユニット間の直接通信を可能にします。


##### <a name="direct-access-from-on-premises"></a>オンプレミスからの直接アクセス
Global Reach が提供されている Azure リージョンでは、ExpressRoute 回線用に Global Reach の有効化を要求できます。 この回線は、オンプレミス ネットワークを、HANA Large Instances に接続する Azure 仮想ネットワークに接続します。 ExpressRoute 回線のオンプレミス側には、お客様のコストがかかります。 詳細については、[Global Reach アドオン](https://azure.microsoft.com/pricing/details/expressroute/)の価格を確認してください。 HANA Large Instances を Azure に接続する回線に追加料金はありません。 

> [!IMPORTANT]  
> Global Reach を使用して HANA Large Instances ユニットとオンプレミスの資産との間の直接アクセスを有効にする場合、ネットワークのデータと制御フローは、**Azure 仮想ネットワークを経由してルーティングされません**。 代わりに、ネットワーク データと制御フローは、Microsoft エンタープライズ Exchange ルーター間で直接ルーティングされます。 そのため、Azure 仮想ネットワークにデプロイしたすべての NSG または ASG の規則、すべての種類のファイアウォール、NVA、またはプロキシに影響を受けることはありません。 **ExpressRoute Global Reach を使用してオンプレミスから HANA Large Instances ユニットへの直接アクセスを有効にする場合、HANA L インスタンス ユニットへのアクセスに対する制限および許可は、オンプレミス側のファイアウォールで定義する必要があります。** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>異なる Azure リージョン内の HANA L インスタンスの接続
同様に、ExpressRoute Global Reach を使用すると、異なるリージョンにデプロイされた 2 つの HANA Large Instances テナントを接続できます。 分離の機能を果たしているのは、HANA Large Instances テナントが両方のリージョンの Azure への接続に使用する ExpressRoute 回線です。 異なるリージョンにデプロイされた 2 つの HANA Large Instances テナントを接続するための追加の料金はかかりません。 

> [!IMPORTANT]  
> HANA Large Instances テナント間のネットワーク トラフィックのデータ フローおよび制御フローは、Azure ネットワークを通じてルーティングされません。 そのため、Azure の機能やネットワーク仮想アプライアンス (NVA) を使用して、HANA Large Instances テナント間の通信制限を強制することはできません。 

ExpressRoute Global Reach を有効にする方法の詳細については、「[HANA Large Instances に仮想ネットワークを接続する](./hana-connect-vnet-express-route.md)」を参照してください。


## <a name="internet-connectivity-of-hana-large-instance"></a>HANA L インスタンスのインターネット接続
HANA Large Instances には、インターネットとの間に直接的な接続が *ありません*。 たとえば、この制限によって、OS イメージを OS ベンダーに直接登録することができない可能性があります。 ローカルの SUSE Linux Enterprise Server Subscription Management Tool サーバーや Red Hat Enterprise Linux Subscription Manager を使用することが必要になる場合があります。

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>VM と HANA L インスタンス間でのデータの暗号化
HANA Large Instances と VM の間で転送されるデータは暗号化されません。 ただし、HANA DBMS 側と JDBC/ODBC ベースのアプリケーションとの間でのデータ交換のみを目的としている場合は、トラフィックの暗号化を有効にすることができます。 詳細については、[SAP HANA と JDBC クライアントまたは ODBC クライアントと間の通信のセキュリティ保護](https://help.sap.com/viewer/102d9916bf77407ea3942fef93a47da8/1.0.11/en-US/dbd3d887bb571014bf05ca887f897b99.html)に関する記事を参照してください。

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>複数のリージョンでの HANA L インスタンス ユニットの使用

ディザスター リカバリーの場合、HANA Large Instances ユニットを複数の Azure リージョンに配置する必要があります。 Azure [グローバル Vnet ピアリング](../../../virtual-network/virtual-network-peering-overview.md)のみを使用すると、既定では、異なるリージョン内の HANA Large Instances テナント間の推移的なルーティングは機能しません。 ただし、Global Reach は、異なるリージョン内の HANA Large Instances ユニット間の通信を開きます。 ExpressRoute Global Reach を使用するこのシナリオにより、次のことが可能になります。

 - プロキシまたはファイアウォールを追加しない HANA システム レプリケーション。
 - システム コピーまたはシステム更新を行うための、異なるリージョン内の HANA Large Instances ユニット間でのバックアップのコピー。


[![異なる Azure リージョン内の Azure L インスタンス スタンプに接続された仮想ネットワーク](./media/hana-overview-architecture/image8-multiple-regions.png)](./media/hana-overview-architecture/image8-multiple-regions.png#lightbox)

前の図は、両方のリージョン内の仮想ネットワークが、2 つの ExpressRoute 回線に接続されるしくみを示しています。 この回線は、両方の Azure リージョン (グレーの線) 内の SAP HANA on Azure (Large Instances) への接続に使用されます。 この 2 つの交差接続の理由は、両側の MSEE を機能停止から保護することです。 2 つの Azure リージョン内の 2 つの仮想ネットワーク間の通信フローは、2 つの異なるリージョン内の 2 つの仮想ネットワークの[グローバル ピアリング](/archive/blogs/azureedu/how-to-setup-global-vnet-peering-in-azure) (青い点線) で処理されることになっています。 太い赤線は、ExpressRoute Global Reach の接続を示します。 この接続により、異なるリージョン内のテナントの HANA Large Instance ユニットが相互に通信できます。 

> [!IMPORTANT] 
> 複数の ExpressRoute 回線を使用する場合は、トラフィックを適切にルーティングできるように、AS パス プリペンドとローカル プリファレンス BGP の設定を使用します。

## <a name="next-steps"></a>次のステップ

SAP HANA (Large Instance) のストレージ アーキテクチャについて説明します。

> [!div class="nextstepaction"]
> [SAP HANA (Large Instances) のストレージ アーキテクチャ](hana-storage-architecture.md)
