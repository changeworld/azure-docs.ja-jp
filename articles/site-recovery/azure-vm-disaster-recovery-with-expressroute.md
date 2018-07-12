---
title: Azure 仮想マシンのディザスター リカバリーに ExpressRoute を使用する | Microsoft Docs
description: Azure ExpressRoute と Azure Site Recovery を使って Azure 仮想マシンのディザスター リカバリーを行う方法を説明します
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: manayar
ms.openlocfilehash: 73514b524f554affb9730ba63ccd608491497af2
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2018
ms.locfileid: "37920472"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Azure 仮想マシンのディザスター リカバリーに ExpressRoute を使用する

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供するプライベート接続で、オンプレミスのネットワークを Microsoft Cloud に拡張できます。 この記事では、Azure 仮想マシンのディザスター リカバリーに ExpressRoute と Site Recovery を使用する方法について説明します。

## <a name="prerequisites"></a>前提条件

開始する前に、次の事柄について理解していることを確認してください。
-   ExpressRoute [回線](../expressroute/expressroute-circuit-peerings.md)
-   ExpressRoute の[ルーティング ドメイン](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
-   Azure 仮想マシンの[レプリケーション アーキテクチャ](azure-to-azure-architecture.md)
-   Azure 仮想マシンの[レプリケーションの設定](azure-to-azure-tutorial-enable-replication.md)
-   Azure 仮想マシンの[フェールオーバー](azure-to-azure-tutorial-failover-failback.md)

## <a name="expressroute-and-azure-virtual-machine-replication"></a>ExpressRoute と Azure 仮想マシンのレプリケーション

Site Recovery を使って Azure 仮想マシンを保護する場合、レプリケーション データは、[Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md) を使用しているかどうかに応じて、Azure Storage アカウントか、ターゲット Azure リージョンのレプリカ マネージド ディスクに送信されます。 レプリケーションのエンドポイントはパブリックですが、Azure VM のレプリケーション トラフィックは、ソース仮想ネットワークがどの Azure リージョンに存在するかに関わらず、既定ではインターネットを経由しません。

Azure VM のディザスター リカバリーについては、レプリケーション データが Azure の境界を出ることはないため、レプリケーションに ExpressRoute は必要ありません。 仮想マシンがターゲットの Azure リージョンにフェールオーバーされたら、[プライベート ピアリング](../expressroute/expressroute-circuit-peerings.md#azure-private-peering)を使ってそれらのマシンにアクセスできます。

## <a name="replicating-azure-deployments"></a>Azure デプロイのレプリケート

前の[記事](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)では、ExpressRoute を通じてお客様のオンプレミス データ センターに接続された、単一の Azure 仮想ネットワークでの簡単な設定について説明しました。 企業の一般的なデプロイでは、複数の Azure 仮想ネットワークにワークロードが分割され、中央の接続ハブによって、インターネットとオンプレミスの両方のデプロイに対する外部接続が確立されます。

この例では、企業のデプロイで広く使用される、ハブ アンド スポーク トポロジについて説明します。
-   デプロイは **Azure 東アジア** リージョンにあり、オンプレミス データ センターには、香港のパートナー エッジを介した ExpressRoute 回線接続が使用されています。
-   アプリケーションは 2 つのスポーク仮想ネットワークを使用してデプロイされています。アドレス空間 10.1.0.0/24 を使用した**ソース VNet1** と、アドレス空間 10.2.0.0/24 を使用した**ソース VNet2** です。
-   ハブ仮想ネットワーク (アドレス空間 10.10.10.0/24 を使用した**ソース ハブ VNet**) は、ゲートキーパーとして機能します。 サブネット間でのすべての通信は、このハブを通過します。
-   ハブ仮想ネットワークには、2 つのサブネットがあります。アドレス空間 10.10.10.0/25 を使用した **NVA サブネット**と、アドレス空間 10.10.10.128/25 を使用した**ゲートウェイ サブネット**です。
-   **NVA サブネット**には、IP アドレス 10.10.10.10 を使用したネットワーク仮想アプライアンスがあります。
-   **ゲートウェイ サブネット**には、ExpressRoute 接続に接続された ExpressRoute ゲートウェイがあります。これは、プライベート ピアリングのルーティング ドメインを通じてお客様のオンプレミス データ センターへとルーティングされます。
-   各スポーク仮想ネットワークはハブ仮想ネットワークに接続され、このネットワーク トポロジ内でのすべてのルーティングは、Azure のルート テーブル (UDR) を通じて制御されます。 1 つの VNet から他の VNet への送信トラフィックや、オンプレミス データ センターへの送信トラフィックはすべて、NVA を介してルーティングされます。

![ExpressRoute を使用したオンプレミスと Azure の接続 (フェールオーバー前)](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>ハブ アンド スポーク ピアリング

スポークからハブへのピアリングには、次の構成が使用されています。
-   仮想ネットワーク アクセスを許可する: 有効
-   転送されたトラフィックを許可する: 有効
-   ゲートウェイ転送を許可する: 無効
-   リモート ゲートウェイを使用する: 有効

 ![スポークからハブへのピアリング構成](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

ハブからスポークへのピアリングには、次の構成が使用されています。
-   仮想ネットワーク アクセスを許可する: 有効
-   転送されたトラフィックを許可する: 有効
-   ゲートウェイ転送を許可する: 有効
-   リモート ゲートウェイを使用する: 無効

 ![ハブからスポークへのピアリング構成](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>デプロイのレプリケーションを有効にする

上記の設定を行うには、まず Site Recovery を使用してすべての仮想マシンの[ディザスター リカバリーを設定](azure-to-azure-tutorial-enable-replication.md)します。 Site Recovery では、ターゲット リージョンにレプリカ仮想ネットワーク (サブネットとゲートウェイ サブネットを含む) を作成し、ソースとターゲットの仮想ネットワーク間に必要なマッピングを作成することができます。 また、ターゲット側のネットワークとサブネットを事前に作成して、レプリケーションを有効化する際に同じものを使用することもできます。

Site Recovery では、ルート テーブル、仮想ネットワーク ゲートウェイ、仮想ネットワーク ゲートウェイ接続、仮想ネットワーク ピアリング、およびその他のネットワーク リソースや接続はレプリケートされません。 これらと、[レプリケーション プロセス](azure-to-azure-architecture.md#replication-process)に含まれないその他のリソースは、フェールオーバー時またはフェールオーバー前に作成し、関連するリソースに接続する必要があります。 Azure Site Recovery の強力な[復旧計画](site-recovery-create-recovery-plans.md)を使用すれば、自動化スクリプトによって、追加リソースの作成と接続を自動化できます。

既定では、レプリケーション トラフィックが Azure の境界を出ることはありません。 通常は、NVA デプロイでも、送信インターネット トラフィックが NVA を通過する、既定のルート (0.0.0.0/0) が定義されます。 この場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスの速度が制限される可能性があります。 すべての Azure VM トラフィックをオンプレミス デプロイにルーティングする既定のルートを使用した場合も、同じことが起こる可能性があります。 "ストレージ" 用の仮想ネットワーク内に[仮想ネットワーク サービス エンドポイントを作成](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)して、レプリケーション トラフィックが Azure 境界の外に出ないようにすることをお勧めします。

## <a name="failover-models-with-expressroute"></a>ExpressRoute を使ったフェールオーバー モデル

Azure 仮想マシンが別のリージョンにフェールオーバーされる際、ソース仮想ネットワークへの既存の ExpressRoute 接続が、回復リージョンのターゲット仮想ネットワークへと自動的に転送されることはありません。 新しい接続によって、ExpressRoute をターゲット仮想ネットワークに接続する必要があります。

Azure 仮想マシンは、同じ地理クラスター内の任意の Azure リージョンにレプリケートできます (詳しくは[こちら](azure-to-azure-support-matrix.md#region-support)をご覧ください)。 選択したターゲット Azure リージョンがソースと同じ地理的リージョン内になく、ソースとターゲットのリージョン接続に単一の ExpressRoute 回線を使用している場合は、ExpressRoute Premium を有効にする必要があります。 詳しくは、「[ExpressRoute の場所](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region)」と「[ExpressRoute の価格](https://azure.microsoft.com/pricing/details/expressroute/)」をご覧ください。

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>2 つの異なる ExpressRoute ピアリング場所に 2 つの ExpressRoute 回線を使用する
-   この構成は、プライマリ ExpressRoute 回線の障害や大規模な地域障害に備える必要がある場合に便利です (これらの障害が起きた場合、ExpressRoute ピアリング場所に影響が及び、プライマリ ExpressRoute 回線が混乱する可能性があります)。
-   通常、運用環境に接続された回線はプライマリ回線として使われます。セカンダリ回線はフェール セーフされ、通常は低い帯域幅が設定されます。 障害イベント時にセカンダリをプライマリに切り替える必要がある場合は、セカンダリの帯域幅を増やすことができます。
-   この構成では、フェールオーバー後にセカンダリ ExpressRoute 回線からターゲット仮想ネットワークへの接続を確立したり、障害宣言に備えた接続を確立して、全体的な回復時間を短縮することができます。 プライマリとターゲット リージョンの両方の仮想ネットワークに対する同時接続を使用する場合は、フェールオーバーが完了するまで、オンプレミスのルーティングにセカンダリの回線や接続が使用されないようになっていることを確認してください。
-   Site Recovery によって保護されている VM の仮想ネットワーク (ソースおよびターゲット) には、要件に応じて、フェールオーバー時に同じ IP アドレスを使うこともできますし、異なる IP アドレス使うこともできます。 どちらの場合も、セカンダリ接続はフェールオーバーの前に確立できます。

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>同じ ExpressRoute ピアリング場所に 2 つの ExpressRoute 回線を使用する
-   この構成では、プライマリ ExpressRoute 回線の障害に備えることはできますが、大規模な地域障害に備えることはできません (障害が起きた場合、ExpressRoute ピアリング場所に影響が及ぶ可能性があります)。 後者の場合、プライマリとセカンダリの両方の回線が影響を受ける可能性があります。
-   IP アドレスと接続に関するその他の条件は、前述のケースと同じです。 オンプレミス データ センターからソース仮想ネットワークへのプライマリ回線と、ターゲット仮想ネットワークへのセカンダリ回線を同時に接続することができます。 プライマリとターゲット リージョンの両方の仮想ネットワークに対する同時接続を使用する場合は、フェールオーバーが完了するまで、オンプレミスのルーティングにセカンダリの回線や接続が使用されないようになっていることを確認してください。
-   回線が同じピアリング場所で作成されてい場合、両方の回線を同じ仮想ネットワークに接続することはできません。

### <a name="single-expressroute-circuit"></a>単一の ExpressRoute 回線を使う
-   この構成では、大規模な地域障害に備えることはできません (障害が起きた場合、ExpressRoute ピアリング場所に影響が及ぶ可能性があります)。
-   単一の ExpressRoute 回線では、ターゲット リージョンで同じ IP アドレス空間が使用されている場合、ソースとターゲットの仮想ネットワークを回線に同時接続することができません。
-   ターゲット リージョンで同じ IP アドレス空間が使用されている場合は、ソース側の接続を切断し、その後、ターゲット側の接続を確立する必要があります。 この接続変更は、回復計画の一部としてスクリプト化することができます。
-   地域障害の発生時には、プライマリ リージョン へのアクセスができないと、切断操作が失敗する可能性があります。 このような障害の発生時には、ターゲット仮想ネットワークで同じ IP アドレス空間が使用されていると、ターゲット リージョンへの接続作成に影響が及ぶ可能性があります。
-   ターゲットでの接続作成が成功し、その後プライマリ リージョンが回復しても、2 つの接続が同じアドレス空間に同時に接続しようとすると、パケット ドロップが発生する可能性があります。 パケット ドロップを防ぐには、プライマリ接続を即座に終了する必要があります。 仮想マシンがプライマリ リージョンにフェールバックされた後、セカンダリ接続が切断されたら、プライマリ接続を再度確立できます。
-   ターゲット仮想ネットワークで別のアドレス空間が使用されている場合は、同じ ExpressRoute 回線から、ソースとターゲットの仮想ネットワークに同時に接続できます。

## <a name="recovering-azure-deployments"></a>Azure デプロイの回復
フェールオーバー モデルを検討する際には、2 つの異なるピアリング場所に 2 つの異なる ExpressRoute 回線を使用し、保護された Azure 仮想マシン用にプライベート IP アドレスを保有することを検討してください。 ターゲット回復リージョンは Azure 東南アジアで、セカンダリ ExpressRoute 回線接続は、シンガポールにあるパートナー エッジを通じて確立されます。

デプロイ全体の回復を自動化するには、仮想マシンと仮想ネットワークのレプリケートに加えて、関連するその他のネットワーク リソースや接続も作成する必要があります。 前述のハブ アンド スポーク ネットワーク トポロジを使用する場合は、[フェールオーバー](azure-to-azure-tutorial-failover-failback.md)操作の実行時または実行後に、次の追加手順を実行する必要があります。
1.  ターゲット リージョンのハブ仮想ネットワークに、Azure ExpressRoute ゲートウェイを作成します。 ExpressRoute ゲートウェイは、ターゲットのハブ仮想ネットワークを ExpressRoute 回線に接続するために必要です。
2.  ターゲットのハブ仮想ネットワークからターゲットの ExpressRoute 回線への仮想ネットワーク接続を作成します。
3.  ターゲット リージョンのハブ アンド スポーク仮想ネットワーク間に、VNet ピアリングを設定します。 ターゲット リージョンのピアリング プロパティは、ソース リージョンのものと同じになります。
4.  ハブ VNet と、2 つのスポーク Vnet に、UDR を設定します。 ターゲット側の UDR のプロパティは、同じ IP アドレスを使用している場合、ソース側のものと同じです。 異なるターゲット IP アドレスを使用する場合は、UDR を適切に変更する必要があります。

上記の手順は、[回復計画](site-recovery-create-recovery-plans.md)の一部としてスクリプト化することができます。 アプリケーションの接続性や回復時間の要件によっては、上記の手順をフェールオーバーの開始前に完了することもできます。

仮想マシンが回復され、その他の接続手順が完了すると、回復環境は次のようになります。![ExpressRoute を使ったオンプレミスから Azure へのフェールオーバー](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

ExpressRoute 回線を使い、ターゲット仮想マシンに同じ IP アドレスを使った、Azure VM ディザスター リカバリーのシンプルなトポロジ例については、[こちら](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity)をご覧ください。

## <a name="recovery-time-objective-rto-considerations"></a>目標復旧時間 (RTO) に関する考慮事項
デプロイの全体的な回復時間を短縮するには、事前にターゲット リージョンの追加の[ネットワーク コンポーネント](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) (仮想ネットワーク ゲートウェイなど) をプロビジョニングし、デプロイすることをお勧めします。 ダウンタイムを短縮できるかどうかは、追加リソースのデプロイによって左右されます。計画時にダウンタイムを十分考慮しておかないと、ダウンタイムが全体的な回復時間に影響する可能性があります。

保護されたデプロイについては、[ディザスター リカバリー訓練](azure-to-azure-tutorial-dr-drill.md)を定期的に実施することをお勧めします。 訓練ではデータ損失やダウンタイムなしでレプリケーション戦略を検証します。これは運用環境には影響しません。 また、訓練を実施しておけば、直近の構成問題によって回復時刻の目標達成に悪影響が及ぶことも回避できます。 レプリケーションを有効にしたときに設定された既定のネットワークではなく、テスト フェールオーバー用の別個の Azure VM ネットワークを使用することをお勧めします。

単一の ExpressRoute 回線を使用している場合は、地域障害の際に接続確立の問題が発生するのを回避するため、ターゲット仮想ネットワークに異なる IP アドレス空間を使用することをお勧めします。 異なる IP アドレスを使用することが回復対象の運用環境において困難な場合は、ディザスター リカバリー訓練の際、異なる IP アドレスを使った個別のテスト ネットワークでテスト フェールオーバーを実施してください。IP アドレス空間が重複する 2 つの仮想ネットワークを同じ ExpressRoute 回線に接続することはできません。

## <a name="next-steps"></a>次の手順
- [ExpressRoute 回線](../expressroute/expressroute-circuit-peerings.md)について詳しく学習する。
- [ExpressRoute ルーティング ドメイン](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)について詳しく学習する。
- [ExpressRoute の場所](../expressroute/expressroute-locations.md)について詳しく学習する。
- アプリケーションのフェールオーバーを自動化するための[復旧計画](site-recovery-create-recovery-plans.md)の詳細を確認する。
