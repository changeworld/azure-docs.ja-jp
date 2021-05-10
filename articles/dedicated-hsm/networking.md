---
title: ネットワークについての考慮事項 - Azure 専用 HSM | Microsoft Docs
description: Azure 専用 HSM のデプロイに関連するネットワークの考慮事項の概要
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 3370389027805cfb5a68b5b0551d14dc31154804
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611839"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure の専用 HSM のネットワーク

Azure の専用 HSM は、きわめて安全なネットワーク環境を必要とします。 分散アプリケーションを使用して Azure クラウドからお客様の IT 環境 (オンプレミス) にアクセスする場合であれ、高可用性のシナリオであれ、このことは変わりません。 これは Azure のネットワークによって提供されるので、対処すべき領域は 4 つです。

- Azure の仮想ネットワーク (VNet) 内に HSM デバイスを作成する
- HSM デバイスの構成と管理のためにオンプレミスからクラウドベース リソースに接続する
- 相互接続するアプリケーション リソースと HSM デバイスのための仮想ネットワークを作成して接続する
- 相互通信や、高可用性のシナリオに対応するための仮想ネットワークをリージョンの境界を越えて接続する

## <a name="virtual-network-for-your-dedicated-hsms"></a>専用 HSM のための仮想ネットワーク

専用 HSM は仮想ネットワークに統合され、Azure 内のお客様専用のプライベート ネットワークに配置されます。 これにより、仮想ネットワーク内の仮想マシンやコンピューティング リソースから、このデバイスにアクセスすることができます。  
仮想ネットワークへの Azure サービスの統合と、それによって得られる機能の詳細については、[Azure サービス向けの仮想ネットワーク](../virtual-network/virtual-network-for-azure-services.md)に関するドキュメントを参照してください。

### <a name="virtual-networks"></a>仮想ネットワーク

専用 HSM デバイスをプロビジョニングする前に、お客様はまず Azure に仮想ネットワークを作成するか、お客様のサブスクリプションに既にある仮想ネットワークを使用する必要があります。 仮想ネットワークによって、専用 HSM デバイスのセキュリティ境界が定義されます。 仮想ネットワークの作成について詳しくは、[仮想ネットワークのドキュメント](../virtual-network/virtual-networks-overview.md)を参照してください。

### <a name="subnets"></a>サブネット

サブネットは、仮想ネットワークを個別のアドレス空間に分割し、サブネット内に配置された Azure リソースから使用できるようにするものです。 専用 HSM は、仮想ネットワークのサブネットにデプロイされます。 お客様のサブネットにデプロイされた専用 HSM デバイスにはそれぞれ、このサブネットからプライベート IP アドレスが割り当てられます。 HSM デバイスのデプロイ先となるサブネットは、次のサービスに対して明示的に委任されている必要があります。Microsoft.HardwareSecurityModules/dedicatedHSMs。 これにより、サブネットへのデプロイに使用される特定のアクセス許可が HSM サービスに与えられます。 専用 HSM への委任に伴い、サブネットに対していくらかポリシーの制限が生じます。 現在、委任されたサブネットでは、ネットワーク セキュリティ グループ (NSG) とユーザー定義ルート (UDR) がサポートされません。 したがって、サブネットを専用 HSM に委任すると、そのサブネットは HSM リソースのデプロイにしか使用できなくなります。 お客様の他のリソースをサブネットにデプロイしようとしても失敗します。


### <a name="expressroute-gateway"></a>ExpressRoute ゲートウェイ

現在のアーキテクチャにはある要件があり、HSM デバイスを Azure に統合できるようにするには、HSM デバイスの配置先となるお客様のサブネットに ER ゲートウェイを構成する必要があります。 Azure にあるお客様の HSM デバイスにオンプレミスの場所を接続する目的でこの ER ゲートウェイを利用することはできません。

## <a name="connecting-your-on-premises-it-to-azure"></a>オンプレミスの IT を Azure に接続する

クラウドベースのリソースを作成する場合は、通常、オンプレミスの IT リソースへのプライベート接続が必要となります。 専用 HSM のケースでは、この接続は主に HSM クライアント ソフトウェアが HSM デバイスを構成する目的で使用するほか、分析に使用するログを HSM から取得する作業やバックアップにも使用されます。 ここで重要な意思決定のポイントとなるのが接続の特性であり、いくつかの選択肢があります。  最も柔軟性の高い選択肢はサイト間 VPN です。Azure クラウド内のリソース (HSM を含む) との安全な通信を必要とするオンプレミス リソースは複数存在する可能性が高いためです。 この場合、お客様の組織には、接続を容易にする VPN デバイスが必要になります。 オンプレミスにエンドポイントが 1 つしか存在しなければ (単一の管理ワークステーションなど)、ポイント対サイト VPN 接続を使用できます。
接続の選択肢の詳細については、[VPN Gateway の計画の選択肢](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)に関するセクションを参照してください。

> [!NOTE]
> 現時点では、オンプレミス リソースへの接続に ExpressRoute を選択することはできません。 また、前述した ExpressRoute ゲートウェイは、オンプレミス インフラストラクチャに接続するためのものではないので注意してください。

### <a name="point-to-site-vpn"></a>ポイント対サイト VPN

ポイント対サイト仮想プライベート ネットワークは、オンプレミスの単一エンドポイントに安全に接続するうえで最もシンプルな形態です。 Azure ベースの専用 HSM に対して単一の管理ワークステーションだけを使用する場合は、これが適している可能性があります。

### <a name="site-to-site-vpn"></a>サイト対サイト VPN

サイト間仮想プライベート ネットワークなら、Azure ベースの専用 HSM とオンプレミスの IT 環境との間で安全な通信を実現できます。 これを行う理由は、HSM のオンプレミス用のバックアップ設備を設けることです。また、バックアップを実行するためには両者の間に接続が必要であるためでもあります。

## <a name="connecting-virtual-networks"></a>仮想ネットワークどうしを接続する

専用 HSM の一般的なデプロイ アーキテクチャの原型となるものは、1 つの仮想ネットワークと、HSM デバイスを作成してプロビジョニングする先の、対応するサブネットです。 その専用 HSM を利用することになるアプリケーション コンポーネント用に、さらに別の仮想ネットワークとサブネットを、その同じリージョン内に追加することができます。 これらのネットワークをまたいで通信を実現するためには、仮想ネットワーク ピアリングを使用します。

### <a name="virtual-network-peering"></a>仮想ネットワーク ピアリング

1 つのリージョン内に、互いのリソースへのアクセスを必要とする複数の仮想ネットワークが存在する場合、仮想ネットワーク ピアリングを使用することで両者の間に安全な通信チャネルを作成できます。  仮想ネットワーク ピアリングによって、安全な通信が実現するだけでなく、Azure 内のリソース間に短い待ち時間と高帯域幅接続が確保されます。

![ネットワーク ピアリング](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Azure リージョンの境界を越えて接続する

HSM デバイスには、ソフトウェア ライブラリを通じて、トラフィックを別の HSM にリダイレクトする機能があります。 トラフィックのリダイレクトは、デバイスが故障したり、デバイスへのアクセスが失われたりした場合に役立ちます。 リージョン レベルで発生する障害のシナリオは、HSM を他のリージョンにデプロイし、リージョンの境界を越えて通信を仮想ネットワーク間に確保することで軽減できます。

### <a name="cross-region-ha-using-vpn-gateway"></a>VPN Gateway を使用したリージョン間 HA

グローバルに分散されたアプリケーションや、フェールオーバーをリージョン間で行う高可用性のシナリオでは、リージョンの境界を越えて仮想ネットワークを接続する必要があります。 Azure の専用 HSM では、2 つの仮想ネットワーク間に安全なトンネルを提供する VPN ゲートウェイを使用して高可用性を確保することができます。 VPN Gateway を使用した VNet 間接続の詳細については、「[VPN ゲートウェイとは](../vpn-gateway/design.md#V2V)」という記事を参照してください。

> [!NOTE]
> 現時点では、専用 HSM を使用したリージョン間接続のシナリオで、グローバル VNET ピアリングを利用することはできません。VPN Gateway を使用する必要があります。 

![2 つの VPN ゲートウェイで接続されている 2 つのリージョンを示す図。 各リージョンに、ピアリングされた仮想ネットワークが含まれています。](media/networking/global-vnet.png)

## <a name="networking-restrictions"></a>ネットワークの制限
> [!NOTE]
> サブネット委任を使用した Dedicated HSM サービスの制約は、HSM デプロイのターゲット ネットワーク アーキテクチャを設計するときに考慮すべき強制的な制限です。 サブネット委任の使用は、NSG、UDR、グローバル VNet ピアリングが、Dedicated HSM でサポートされていないことを意味します。 以下のセクションでは、これらの機能と同じかまたは類似の結果を達成する代替の手法に関するヘルプを提供します。 

Dedicated HSM VNet に存在する HSM NIC では、ネットワーク セキュリティ グループまたはユーザー定義ルートを使用できません。 つまり、Dedicated HSM VNet の観点から既定の拒否ポリシーを設定することはできません。また、Dedicated HSM サービスにアクセスするには、他のネットワーク セグメントを許可リストに載せる必要があります。 

ネットワーク仮想アプライアンス (NVA) プロキシ ソリューションを追加することで、トランジットまたは DMZ ハブの NVA ファイアウォールを HSM NIC の前に論理的に配置できるようになるため、NSG と UDR の必要な代替手段を提供することもできます。

### <a name="solution-architecture"></a>ソリューションのアーキテクチャ
このネットワーク設計には、次の要素が必要です。
1.  NVA プロキシ層があるトランジットまたは DMZ ハブ VNet。 複数の NVA が存在するのが理想的です。 
2.  プライベート ピアリングが有効になっており、トランジット ハブ VNet への接続がある ExpressRoute 回線。
3.  トランジット ハブ VNet と Dedicated HSM VNet 間の VNet ピアリング。
4.  NVA ファイアウォールまたは Azure Firewall をデプロイし、ハブで DMZ サービスをオプションとして提供できます。 
5.  追加のワークロード スポーク VNet をハブ VNet とピアリングできます。 Gemalto クライアントでは、ハブ VNet を介して Dedicated HSM サービスにアクセスできます。

![図は、NSG と UDR の回避策として NVA プロキシ層がある DMZ ハブ VNet を示しています](media/networking/network-architecture.png)

NVA プロキシ ソリューションを追加すると、トランジットまたは DMZ ハブ内の NVA ファイアウォールを HSM NIC の前に論理的に配置できるようになるため、必要な既定の拒否ポリシーが提供されます。 この例では、この目的のために Azure Firewall を使用し、次の要素を設定する必要があります。
1. DMZ ハブ VNet のサブネット "AzureFirewallSubnet" にデプロイされた Azure Firewall
2. Azure ILB プライベート エンドポイントに向かうトラフィックを Azure Firewall に送信する UDR を含むルーティング テーブル。 このルーティング テーブルは、ユーザーの ExpressRoute 仮想ゲートウェイが存在する GatewaySubnet に適用されます
3. 信頼できる発信元範囲と、TCP ポート 1792 でリッスンしている Azure IBL プライベート エンドポイント間の転送を許可する、AzureFirewall 内のネットワーク セキュリティ規則。 このセキュリティ ロジックにより、Dedicated HSM サービスに対して必要な "既定の拒否" ポリシーが追加されます。 つまり、信頼できる発信元 IP 範囲のみが Dedicated HSM サービスへのアクセスを許可されます。 その他のすべての範囲はドロップされます。  
4. オンプレミスに向かうトラフィックを Azure Firewall に送信する UDR を含むルーティング テーブル。 このルーティング テーブルは、NVA プロキシ サブネットに適用されます。 
5. プロキシ NVA サブネットに適用される NSG では、発信元として Azure Firewall のサブネット範囲のみが信頼され、TCP ポート 1792 を介した HSM NIC IP アドレスへの転送のみが許可されます。 

> [!NOTE]
> NVA プロキシ層では、HSM NIC に転送するときにクライアント IP アドレスが SNAT されるため、HSM VNet と DMZ ハブ VNet の間に UDR が必要ありません。  

### <a name="alternative-to-udrs"></a>UDR の代替手段
上で説明した NVA 層ソリューションは、UDR の代替手段として機能します。 注意すべき重要な点がいくつかあります。
1.  NVA でネットワーク アドレス変換を構成して、戻りのトラフィックが正しくルーティングされるようにする必要があります。
2. NAT のために VNA を使用するには、ユーザーが、Luna HSM 構成でクライアント IP チェックを無効にする必要があります。 次のコマンドを例として使用します。
```
Disable:
[hsm01] lunash:>ntls ipcheck disable
NTLS client source IP validation disabled
Command Result : 0 (Success)

Show:
[hsm01] lunash:>ntls ipcheck show
NTLS client source IP validation : Disable
Command Result : 0 (Success)
```
3.  NVA 層へのイグレス トラフィック用に UDR をデプロイします。 
4. 設計に従い、HSM サブネットではプラットフォーム層への送信接続要求が開始されません。

### <a name="alternative-to-using-global-vnet-peering"></a>グローバル VNET ピアリングの使用の代替手段
グローバル VNet ピアリングの代わりとして使用できるいくつかのアーキテクチャがあります。
1.  [VNet 間 VPN Gateway 接続](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)を使用します 
2.  ER 回線で HSM VNET と別の VNET を接続します。 これは、直接のオンプレミスのパスが必要な場合、または VPN VNET に最適です。 

#### <a name="hsm-with-direct-express-route-connectivity"></a>Express Route 直接接続がある HSM
![図は、Express Route 直接接続がある HSM を示しています](media/networking/expressroute-connectivity.png)

## <a name="next-steps"></a>次のステップ

- [よく寄せられる質問](faq.md)
- [サポート可能性](supportability.md)
- [高可用性](high-availability.md)
- [物理的なセキュリティ](physical-security.md)
- [Monitoring](monitoring.md)
- [デプロイ アーキテクチャ](deployment-architecture.md)
