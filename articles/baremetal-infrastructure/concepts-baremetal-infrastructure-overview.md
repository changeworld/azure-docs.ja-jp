---
title: Azure の BareMetal インフラストラクチャの概要
description: Azure の BareMetal インフラストラクチャの概要について説明します。
ms.custom: references_regions
ms.topic: conceptual
ms.subservice: workloads
ms.date: 04/08/2021
ms.openlocfilehash: 7a4998a096a5c5d9e793c34d5046dce59262a2ae
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/09/2021
ms.locfileid: "107257570"
---
#  <a name="what-is-baremetal-infrastructure-on-azure"></a>Azure の BareMetal インフラストラクチャとは

Microsoft Azure では、お客様のビジネス ニーズを満たすためにさまざまなクラウド サービスが統合されたクラウド インフラストラクチャが提供されています。 ただし、場合によっては、仮想化レイヤーを使用せずにベアメタル サーバーでサービスを実行する必要があります。 ルート アクセスとオペレーティング システム (OS) の制御が必要になる場合があります。 このようなニーズを満たすために、Azure では、重要度の高い複数のミッション クリティカルなアプリケーションに対して BareMetal インフラストラクチャが提供されています。

BareMetal インフラストラクチャは、専用の BareMetal インスタンス (コンピューティング インスタンス)、高性能でアプリケーションに適したストレージ (NFS、ISCSI、ファイバー チャネル)、および分離環境の一連の機能固有の仮想 LAN (VLAN) で構成されています。 ストレージは、BareMetal インスタンス間で共有できます。これにより、スケールアウト クラスターや、STONITH を使用した高可用性ペアの作成などの機能が有効になります。
 
この環境には、Azure サブスクリプションの 1 つ以上の Azure 仮想ネットワーク (VNet) で仮想マシン (VM) を実行している場合にアクセスできる特別な VLAN もあります。 この環境全体は、Azure サブスクリプションのリソース グループとして表されます。

BareMetal インフラストラクチャは、30 を超える SKU で、2 ソケットから 24 ソケットのサーバーと 1.5 TB から最大 24 TB までのメモリで提供されます。 Octane メモリでは、大規模な SKU のセットも使用できます。 Azure により、非常に広範囲のベアメタル インスタンスがハイパースケール クラウドで提供されています。

## <a name="why-baremetal-infrastructure"></a>BareMetal インフラストラクチャが選ばれる理由  

企業の一部の主要なワークロードは、一般的な仮想化されたクラウド設定で実行するように設計されていないテクノロジにより構成されています。 これには、特別なアーキテクチャ、認定ハードウェア、または膨大なサイズが必要です。 これらのテクノロジには高度なデータ保護とビジネス継続性の機能がありますが、これらの機能は仮想化されたクラウド用には構築されていません。 これらは待機時間とうるさい隣人の影響を受けやすいため、変更管理とメンテナンス アクティビティをはるかに細かく制御する必要があります。

BareMetal インフラストラクチャは、このような一連のアプリケーション用に構築、認定、テストされています。 このようなソリューションを最初に提供したのは Azure であり、それ以降、大規模なポートフォリオと高度なシステムによって主導し続けています。

BareMetal インフラストラクチャには、これらのベネフィットがあります。 

- 専用のインスタンス
- 専用のワークロード用の認定ハードウェア
    - SAP ([SAP Note #1928533](https://launchpad.support.sap.com/#/notes/1928533) を参照)
    - Oracle ([Oracle ドキュメント ID #948372.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=52088246571495&id=948372.1&_adf.ctrl-state=kwnkj1hzm_52) を参照)
- ベアメタル (コンピューティング仮想化なし)
- Azure でホストされるアプリケーション VM と BareMetal インスタンス間の短い待ち時間 (0.35 ミリ秒)
- すべての Flash SSD と NVMe
    - 最大 1 PB/テナント 
    - 最大 120 万/テナントの IOPS 
    - 40/100 GB のネットワーク帯域幅
    - NFS、ISCSI、FC 経由でアクセス可能
- 冗長性のある電源、電源装置、NIC、TOR、ポート、WAN、ストレージ、および管理
- 障害時に交換するためのホット スペア (再構成の必要なし)
- 顧客が調整できるメンテナンス期間
- アプリケーション対応のスナップショット、アーカイブ、ミラーリング、および複製


## <a name="sku-availability-in-azure-regions"></a>Azure リージョンの SKU の可用性

BareMetal インフラストラクチャには、専用のワークロード用に認定された複数の SKU が用意されています。 ワークロード固有の SKU を使用して、ニーズを満たします。

- Large インスタンス – 2 ソケットから 4 ソケット システムまで。  
- Very Large インスタンス – 4 ソケットから 20 ソケット システムまで。 

専用のワークロードの BareMetal インフラストラクチャは、次の Azure リージョンでご利用いただけます。
- 西ヨーロッパ
- 北ヨーロッパ
- ドイツ中西部 *ゾーンのサポート
- 米国東部 2 *ゾーンのサポート
- 米国東部 *ゾーンのサポート
- 米国西部 *ゾーンのサポート
- 米国西部 2 *ゾーンのサポート
- 米国中南部

>[!NOTE]
>**ゾーンのサポート** とは、高い回復性と可用性のために、ゾーン間で BareMetal インスタンスをデプロイできるリージョン内の可用性ゾーンを指します。 この機能により、マルチサイトのアクティブ/アクティブ スケーリングのサポートが有効になります。

## <a name="managing-baremetal-instances-in-azure"></a>Azure での BareMetal インスタンスの管理 

ニーズによっては、BareMetal インフラストラクチャのアプリケーション トポロジが複雑になることがあります。 1 つ以上の場所に、共有または専用ストレージを持つ複数のインスタンスと特別な LAN および WAN 接続をデプロイする場合があります。 そのため、Azure では、プロビジョニング ポータルのフィールドの CSA または GBB によって、BareMetal インフラストラクチャ用にその情報を相談的に取得することができます。 

BareMetal インフラストラクチャがプロビジョニングされた時点で、OS、ネットワーク、ストレージ ボリューム、ゾーンとリージョン内の配置、場所間の WAN 接続は既に事前構成されています。 OS ライセンス (BYOL) を登録し、OS を構成して、アプリケーション層をインストールする準備が整いました。

Azure portal には、すべての BareMetal インフラストラクチャ リソース、およびその状態と属性が表示されます。 また、そこからインスタンスを操作し、サービス要求とサポート チケットを開くこともできます。 

## <a name="operational-model"></a>運用モデル
BareMetal インフラストラクチャは、ISO 27001、ISO 27017、SOC 1、SOC 2 に準拠しています。 また、次のライセンス持ち込み (BYOL) モデルも使用されます (OS、専用のワークロード、およびサードパーティー製のアプリケーション)。  

ルート アクセスとフル コントロールを受け取るとすぐに、次の責任を負うことになります。
- バックアップと回復のソリューション、高可用性、ディザスター リカバリーの設計と実装。
- OS およびサードパーティ製ソフトウェアのライセンス、セキュリティ、およびサポート。

Microsoft の責任は次のとおりです。
- 専用のワークロードにハードウェアを提供する。 
- OS をプロビジョニングする。

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-support-model.png" alt-text="BareMetal インフラストラクチャ サポート モデルの図。" border="false":::

## <a name="baremetal-instance-stamp"></a>BareMetal インスタンス スタンプ

BareMetal インスタンス スタンプ自体は次のコンポーネントを結合します。

- **コンピューティング:** 必要なコンピューティング機能を提供し、専用のワークロードに対して認定された、Intel Xeon プロセッサの世代に基づくサーバー。

- **ネットワーク:** コンピューティング、ストレージ、および LAN コンポーネントを相互接続する統合された高速ネットワーク ファブリック。

- **ストレージ:** 統合されたネットワーク ファブリック経由でアクセスされるインフラストラクチャ。

BareMetal スタンプのマルチテナント インフラストラクチャ内では、分離されたテナントとしてお客様の環境がデプロイされます。 テナントのデプロイ時に、Azure 加入契約内で Azure サブスクリプションに名前を付けます。 この Azure サブスクリプションは、BareMetal のインスタンスに対して課金されるものです。

>[!NOTE]
>BareMetal インスタンスをデプロイしている顧客は、テナントに分離されます。 テナントは、ネットワーク、ストレージ、およびコンピューティング レイヤーで他のテナントから分離されます。 別のテナントに割り当てられているストレージおよびコンピューティング ユニットは、それらの BareMetal インスタンスで相互に認識したり通信したりすることはできません。

## <a name="operating-system"></a>オペレーティング システム
BareMetal インスタンスのプロビジョニング時に、コンピューターにインストールする OS を選択できます。 

>[!NOTE]
>BareMetal インフラストラクチャは BYOL モデルであることに注意してください。

使用できる Linux OS のバージョンは次のとおりです。
- Red Hat Enterprise Linux (RHEL)
- SUSE Linux Enterprise Server (SLES)

## <a name="storage"></a>ストレージ
BareMetal インフラストラクチャでは、高度に冗長な NFS ストレージとファイバー チャネル ストレージが提供されます。 インフラストラクチャにより、SAP や SQL などのエンタープライズ ワークロードのための緊密な統合が提供されます。 また、アプリケーション整合性のあるデータ保護およびデータ管理機能も用意されています。 セルフサービス管理ツールでは、単一のインターフェイスでの監視とともに、領域効率に優れたスナップショット、複製、および詳細なレプリケーション機能が提供されます。 インフラストラクチャによって、データの可用性とビジネス継続性のニーズのため、RPO と RTO 機能 をゼロにすることができます。 

ストレージ インフラストラクチャでは、次のものが提供されます。
- 最大 4 x 100 GB のアップリンク。
- 最大 32 GB のファイバー チャネル アップリンク。
- すべての Flash SSD と NVMe ドライブ。
- 超低遅延と高スループット。
- 最大 4 PB までの RAW ストレージのスケールアップ。 
- 最大 1100 万の IOPS。

こちらのデータ アクセス プロトコルがサポートされています。 
- iSCSI 
- NFS (v3 または v4) 
- ファイバー チャネル 
- FC 経由の NVMe  

## <a name="networking"></a>ネットワーク
Azure ネットワーク サービスのアーキテクチャは、BareMetal インスタンスに専用のワークロードを正常にデプロイするための重要なコンポーネントです。 すべての IT システムが既に Azure に配置されているとは限りません。 Azure には、オンプレミスのソフトウェア デプロイで Azure を仮想データ センターのように取り扱うためのネットワーク テクノロジが用意されています。 BareMetal インスタンスに必要な Azure ネットワーク機能には次のものが含まれます。

- オンプレミスのネットワーク資産に接続する Azure ExpressRoute 回線に接続された Azure 仮想ネットワーク。
- オンプレミスを Azure に接続する ExpressRoute 回線には、1 Gbps 以上の帯域幅が必要です。
- Azure 内の、または Azure で完全に実行されている拡張 Active Directory と DNS。

ExpressRoute により、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 **ExpressRoute Local** を使用して、オンプレミスの場所と目的の Azure リージョン間でコスト効率の高いデータ転送を行うことができます。 **ExpressRoute Premium** を有効にすると、地理的境界を越えて接続を拡張できます。 

BareMetal インスタンスは、Azure VNet サーバーの IP アドレス範囲内でプロビジョニングされます。

:::image type="content" source="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" alt-text="Azure BareMetal インフラストラクチャ図のアーキテクチャ図。" lightbox="media/concepts-baremetal-infrastructure-overview/baremetal-infrastructure-diagram.png" border="false":::

上記のアーキテクチャは、次の 3 つのセクションに分かれています。
- **左:** パートナーまたはローカルのエッジ ルーター (Equinix など) を介して接続し、さまざまなアプリケーションを実行しているお客様のオンプレミス インフラストラクチャを示します。 詳細については、[接続プロバイダー: Azure ExpressRoute](../expressroute/expressroute-locations.md) に関する記事を参照してください。
- **中央:** Azure エッジ ネットワークへの接続を提供する Azure サブスクリプションを使用してプロビジョニングされた [ExpressRoute](../expressroute/expressroute-introduction.md) を示します。
- **右:** Azure IaaS を示します。この場合、Azure 仮想ネットワーク内でプロビジョニングされるアプリケーションを VM を使用してホストします。
- **下:** 短い待機時間の BareMetal 接続のために [ExpressRoute FastPath](../expressroute/about-fastpath.md) によって有効にされた ExpressRoute ゲートウェイの使用を示します。   
   >[!TIP]
   >これをサポートするには、ExpressRoute ゲートウェイを UltraPerformance にする必要があります。 詳細については、「[ExpressRoute の仮想ネットワーク ゲートウェイについて](../expressroute/expressroute-about-virtual-network-gateways.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

次のステップでは、Azure portal を使用して BareMetal インスタンスを識別および操作する方法について説明します。

> [!div class="nextstepaction"]
> [Azure portal を使用して BareMetal インスタンスを管理する](connect-baremetal-infrastructure.md)
