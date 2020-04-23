---
title: Azure Edge Zone について - プレビュー
description: Microsoft のエッジ コンピューティング オファリングについて説明します。
services: vnf-manager
author: ganesr
ms.service: vnf-manager
ms.topic: article
ms.date: 04/02/2020
ms.author: ganesr
ms.openlocfilehash: aaa849633591bfd34a9fca026c820ec2f9137844
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2020
ms.locfileid: "81410836"
---
# <a name="about-azure-edge-zones---preview"></a>Azure Edge Zone について - プレビュー

Azure Edge Zone は、ユーザーの近くでデータ処理を可能にする Microsoft Azure のオファリング ファミリです。 アプリケーションの低待機時間と高スループットの要件に対応するために、VM、コンテナー、およびその他の選択した Azure サービスを Edge Zone にデプロイできます。

Edge Zone の一般的なユース ケース シナリオは次のとおりです。

- ロボット工学でのリアルタイムの命令と制御
- 人工知能と機械学習によるリアルタイム分析と推論
- マシン ビジョン
- 複合現実および VDI のシナリオのためのリモート レンダリング
- 臨場感あふれるマルチプレイヤー ゲーム
- メディア ストリーミングとコンテンツ配信
- 監視とセキュリティ

Azure Edge Zone には、次の 3 つの独立したオファリングがあります。

- Azure Edge Zone
- Azure Edge Zones with Carrier
- Azure Private Edge Zones

## <a name="azure-edge-zones"></a><a name="edge-zones"></a>Azure Edge Zone

![Edge Zone](./media/edge-zones-overview/edge-zones.png "Edge Zone")

Azure Edge Zone は、Azure リージョンから遠く離れた人口密集地に配置される、フットプリントの小さい Azure の拡張機能です。 Azure Edge Zone は、待機時間の影響を受けやすくスループットの大きいアプリケーションをエンドユーザーの近くで実行できるようにする、VM、コンテナー、および選択した Azure サービスをサポートします。 Azure Edge Zone は Microsoft グローバル ネットワークの一部であり、ユーザーの近くのエッジ ゾーンで実行されているアプリケーションと、Azure リージョン内で実行されているすべての Azure サービスとの間で、セキュリティで保護された、信頼性の高い高帯域幅の接続を提供します。 Azure Edge Zone は Microsoft が所有および運営しており、同じ一連の Azure ツールとポータルを使用して、サービスを管理し、Edge Zone にデプロイすることができます。

一般的なユース ケースは次のとおりです。

- ゲームおよびゲーム ストリーミング
- メディア ストリーミングとコンテンツ配信
- 人工知能と機械学習を使用したリアルタイム分析と推論
- 複合現実のためのレンダリング

Azure Edge Zone は次の都市で利用できます。

- ニューヨーク州ニューヨーク
- カリフォルニア州ロサンゼルス
- フロリダ州マイアミ

詳細については、[Edge Zone チームにお問い合わせください](https://aka.ms/EdgeZones)。

## <a name="azure-edge-zones-with-carrier"></a><a name="carrier"></a>Azure Edge Zones with Carrier

![Edge Zones with Carrier](./media/edge-zones-overview/edge-carrier.png "Edge Zones with Carrier")

Azure Edge Zones with Carrier は、人口密集地にある通信事業者のデータセンター内に配置される、フットプリントの小さい Azure の拡張機能です。 通信事業者インフラストラクチャを備えた Azure Edge Zone は、通信事業者の 5G ネットワークから 1 ホップ離れた場所に配置され、モバイル デバイスからのアプリケーションの待機時間を 10 ミリ秒未満に抑えます。 Azure Edge Zones with Carrier は通信事業者のデータセンターにデプロイされ、Microsoft グローバル ネットワークに接続されます。 これらは、ユーザーの近くで実行されているアプリケーションと、Azure リージョン内で実行されているすべての Azure サービスとの間で、セキュリティで保護された、信頼性の高い高帯域幅の接続を提供します。 開発者は使い慣れた同じツール セットを使用してサービスを構築し、Edge Zone にデプロイすることができます。

一般的なユース ケースは次のとおりです。

- ゲームおよびゲーム ストリーミング
- メディア ストリーミングとコンテンツ配信
- 人工知能と機械学習を使用したリアルタイム分析と推論
- 複合現実のためのレンダリング
- コネクテッドカー
- 遠隔医療

Edge Zone は次の事業者と連携して提供されます。

- AT&T (アトランタ、ダラス、およびロサンゼルス)

## <a name="azure-private-edge-zones"></a><a name="private-edge-zones"></a>Azure Private Edge Zones

![Private Edge Zones](./media/edge-zones-overview/private-edge.png "Private Edge Zones")

Azure Private Edge Zones は、オンプレミスに配置される、フットプリントの小さい Azure の拡張機能です。 Azure Private Edge Zones は [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/) プラットフォームに基づいており、オンプレミスにデプロイされたコンピューティングとストレージ サービスに低待機時間でアクセスできます。 また、Private Edge Zones を使用すると、モバイル パケット コア、ルーター、ファイアウォール、SD-WAN アプライアンスなどの仮想化されたネットワーク機能 (VNF)、および ISV からのアプリケーションを [Azure マネージド アプリケーション](https://azure.microsoft.com/services/managed-applications/)として、オンプレミスの仮想マシンおよびコンテナーと並行してデプロイすることができます。 Azure Private Edge Zones には、クラウドネイティブのオーケストレーション ソリューションが付属しています。これにより、仮想化されたネットワーク機能 (VNF) とアプリケーションのライフサイクルを Azure portal から管理できます。

Azure Private Edge Zones を使用すると、Azure でアプリケーションを構築およびデプロイするときと同じ使い慣れたツールを使用して、アプリケーションをオンプレミスで開発してデプロイすることができます。 また、プライベート モバイル ネットワーク (プライベート LTE、プライベート 5G) やファイアウォールなどのセキュリティ機能を実行し、同じ Private Edge Zone アプライアンス上の SD-WAN アプライアンスを使用して複数のブランチと Azure 全体にわたってオンプレミス ネットワークを拡張し、それらを Azure から管理することもできます。

一般的なユース ケースは次のとおりです。

- ロボット工学でのリアルタイムの命令と制御
- 人工知能と機械学習によるリアルタイム分析と推論
- マシン ビジョン
- 複合現実および VDI のシナリオのためのリモート レンダリング
- 監視とセキュリティ

Private Edge Zones を使用してエンドツーエンドのソリューションを実現する、VNF ベンダー、ISV、および MSP パートナーからなる豊富なエコシステムが用意されています。 詳細については、[Private Edge Zones チームにお問い合わせください](https://aka.ms/EdgeZonesPartner)。

## <a name="private-edge-zones---partners"></a><a name="private-edge-partners"></a>Private Edge Zones - パートナー

![Private Edge Zone のパートナー](./media/edge-zones-overview/partners.png "Private Edge Zone のパートナー")

### <a name="virtualized-network-functions-vnfs"></a><a name="vnf"></a>仮想化されたネットワーク機能 (VNF)

#### <a name="virtualized-evolved-packet-core-vepc-for-mobile-networks"></a><a name="vEPC"></a>モバイル ネットワーク用の仮想化 Evolved Packet Core (vEPC)

- [Affirmed Networks](https://www.affirmednetworks.com/)
- [Druid Software](https://www.druidsoftware.com/)
- [Expeto](https://www.expeto.io/)
- [Mavenir](https://mavenir.com/)
- [Metaswitch](https://www.metaswitch.com/)
- [Nokia Digital Automation Cloud](https://www.dac.nokia.com/)

#### <a name="mobile-radio-partners"></a><a name="mobile-radio"></a>移動無線パートナー

- [Commscope Ruckus](https://support.ruckuswireless.com/)

#### <a name="sd-wan-vendors"></a><a name="sdwan-vendors"></a>SD-WAN ベンダー

- [NetFoundry](https://netfoundry.io/)
- [NuageNetworks (Nokia)](https://www.nuagenetworks.net/)
- [VMware SD-WAN (Velocloud)](https://www.velocloud.com/)

#### <a name="router-vendors"></a><a name="router-vendors"></a>ルーター ベンダー

- [Arista](https://www.arista.com/)

パートナーになるための詳しい方法については、[Private Edge Zones チームにお問い合わせください](https://aka.ms/EdgeZonesPartner)。

#### <a name="firewall-vendors"></a><a name="firewall-vendors"></a>ファイアウォール ベンダー

- Palo Alto Networks

### <a name="managed-solutions-providers---mobile-operators-and-global-system-integrators"></a><a name="msp-mobile"></a>マネージド ソリューション プロバイダー - 通信事業者およびグローバル システム インテグレーター

| グローバル SI および事業者 | 通信事業者 |
| --- | --- |
| Amdocs                       | Etisalat             |
| American Tower               | NTT Communications   |
| Century Link                 | Proximus             |
| Expeto                       | Rogers               |
| Federated Wireless           | SK Telecom           |
| Infosys                      | Telefonica           |
| Tech Mahindra                | Telstra              |
|        *                     | Vodafone             |

パートナーになるための詳しい方法については、[Private Edge Zones チームにお問い合わせください](https://aka.ms/EdgeZonesPartner)。

## <a name="private-edge-zones---solutions"></a><a name="solutions-private-edge"></a>Private Edge Zones - ソリューション

### <a name="private-mobile-network-on-private-edge-zones"></a><a name="private-mobile-private-edge"></a>Private Edge Zones のプライベート モバイル ネットワーク

![Private Edge Zones のプライベート モバイル ネットワーク](./media/edge-zones-overview/mobile-networks.png "Private Edge Zones のプライベート モバイル ネットワーク")

Private Edge Zones にプライベート モバイル ネットワークをデプロイできるようになりました。 プライベート モバイル ネットワークにより、ビジネス クリティカルなアプリケーションに必要な、待機時間が非常に短く高容量で信頼性の高い、セキュリティで保護されたワイヤレス ネットワークを実現できます。 プライベート モバイル ネットワークによって、倉庫内の無人搬送車 (AGV) の命令や制御、スマート ファクトリ内のロボットと拡張現実間のリアルタイム通信、仮想現実エッジ アプリケーションなどのシナリオが可能になります。

仮想化 Evolved Packet Core (vEPC) ネットワーク機能は、プライベート モバイル ネットワークの頭脳を形成します。 Private Edge Zones で vEPC をデプロイできるようになりました。 Private Edge Zones で利用可能な vEPC パートナーの一覧については、[vEPC の ISV](#vEPC) に関する記事を参照してください。

Private Edge Zones にプライベート モバイル ネットワーク ソリューションをデプロイするには、モバイル アクセス ポイントや SIM カード、およびルーターなどの他の VNF などのコンポーネントが必要です。 プライベート モバイル ネットワークを設定するには、ライセンスされた、またはライセンスされていない周波数帯へのアクセスが重要です。 また、RF 計画、物理的なレイアウト、設置、およびサポートに関する支援が必要になる場合があります。 パートナーの一覧については、「[移動無線パートナー](#mobile-radio)」を参照してください。

Microsoft では、ネットワークの計画、必要なデバイスの購入、ハードウェアの設定から、Azure からの構成の管理に至るまで、このプロセスのあらゆる側面を支援するパートナー エコシステムを提供しています。 Microsoft と緊密に統合されている実証済みの一連のパートナーにより、ソリューションの信頼性と使いやすさが保証されます。 自分のコアなシナリオに専念し、他の作業は Microsoft とそのパートナーに任せることができます。

### <a name="sd-wan-on-private-edge-zones"></a><a name="sdwan-private-edge"></a>Private Edge Zones での SD-WAN

![Private Edge Zones での SD-WAN](./media/edge-zones-overview/sd-wan.png "Private Edge Zones での SD-WAN")
 
テクノロジとしての SD-WAN を使用すると、帯域幅の拡大、クラウドへの高パフォーマンス アクセス、サービスの導入、信頼性、ポリシー管理、および広範なネットワーク可視性を備えた、エンタープライズレベルのワイド エリア ネットワーク (WAN) を構築できます。 SD-WAN は、冗長性のある中央コントローラーから調整されたシームレスなブランチ オフィス接続を、低い所有コストで実現します。
Private Edge Zones の SD-WAN により、資本的支出中心モデルから、サービスとしてのソフトウェア (SaaS) モデルに移行し、IT 予算を減らすことができます。 任意の SD-WAN パートナーのオーケストレーターまたコントローラーを使用して、新しいサービスを有効にし、それらをネットワーク全体に直ちに伝達することができます。

## <a name="next-steps"></a>次のステップ

詳細については、次のチームにお問い合わせください。

* [Edge Zone チーム](https://aka.ms/EdgeZones)
* [Private Edge Zones チーム - パートナーになるには](https://aka.ms/EdgeZonesPartner)
