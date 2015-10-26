<properties 
   pageTitle="仮想ネットワークのセキュリティで保護されたクロスプレミス接続について | Microsoft Azure"
   description="サイト間、ポイント対サイト、ExpressRoute 接続などの仮想ネットワークのセキュリティで保護されたクロスプレミス接続の種類について理解する。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2015"
   ms.author="cherylmc" />

# 仮想ネットワークのセキュリティで保護されたクロスプレミス接続について

オンプレミスの サイトを仮想ネットワークに安全に接続するには、[サイト間](#site-to-site-connections)、[ポイント対サイト](#point-to-site-connections)、および [ExpressRoute](#expressroute-connections) の 3 つのオプションがあります。

選択するオプションは、次のようなさまざまな考慮事項によって異なります。


- ソリューションにどのようなスループットが必要か。
- セキュリティで保護された VPN 経由のパブリックなインターネットで通信するか、またはプライベートな接続で通信するか。
- 使用できるパブリック IP アドレスを持っているか。
- VPN デバイスを使用する予定があるか。 その場合、互換性があるか。
- 接続するコンピューターは数台か、サイトとの永続的な接続が必要か。
- 作成するソリューションにはどのような種類の VPN Gateway が必要か。

次の表は、ソリューションに最適な接続オプションを決定するのに役立ちます。

| - | **ポイント対サイト** | **サイト間** | **ExpressRoute - EXP** | **ExpressRoute - NSP** | |------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------| | **Azure でサポートされるサービス** | クラウド サービスの仮想マシン | クラウド サービスの仮想マシン | [サービス一覧](../expressroute/expressroute-faqs.md#supported-azure-services) | [サービス一覧](../expressroute/expressroute-faqs.md#supported-azure-services) | | **一般的な帯域幅** | 一般的には 100 Mbps 未満のアグリゲート | 一般的には 100 Mbps 未満のアグリゲート | 200 Mbps、500 Mbps、1 Gbps および 10 Gbps | 10 Mbps、50 Mbps、100 Mbps、500 Mbps、1 Gbps | | **サポートされるプロトコル** | Secure Sockets Tunneling Protocol (SSTP) | [IPsec](http://go.microsoft.com/fwlink/p/?LinkId=618592) | VLAN を使用した直接接続 | NSP の VPN テクノロジ (MPLS、VPLS、...) | | **ルーティング** | 静的 | 静的 - ポリシー ベース (静的ルーティング) およびルート ベース (動的ルーティング VPN) をサポート | BGP | BGP | | **接続の回復性** | アクティブ-パッシブ | アクティブ-パッシブ | アクティブ-アクティブ | アクティブ-アクティブ | | **一般的な使用例** | プロトタイプの作成、開発/テスト/クラウド サービスおよび仮想マシンのラボ シナリオ | 開発/テスト/クラウド サービスおよび仮想マシンのラボ シナリオおよび小規模実稼動ワークロード | すべての Azure サービス (検証済み一覧)、エンタープライズ クラスおよびミッション クリティカルなワークロード、バックアップ、ビッグデータ、DR サイトとしての Azure へのアクセス | | **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.Microsoft.com/support/legal/sla/) | | **価格** | [価格](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [価格](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [価格](http://azure.microsoft.com/pricing/details/expressroute/) | [価格](http://azure.microsoft.com/pricing/details/expressroute/) | | **テクニカル ドキュメント** | [VPN Gateway ドキュメント](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [VPN Gateway ドキュメント](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [ExpressRoute ドキュメント](https://azure.microsoft.com/documentation/services/expressroute/) | [ExpressRoute ドキュメント](https://azure.microsoft.com/documentation/services/expressroute/) | | **FAQ** | [VPN Gateway の FAQ](vpn-gateway-vpn-faq.md) | [VPN Gateway の FAQ](vpn-gateway-vpn-faq.md) | [ExpressRoute の FAQ](../expressroute/expressroute-faqs.md) | [ExpressRoute の FAQ](../expressroute/expressroute-faqs.md) |
                                                                                 



## サイト間接続

サイト間 VPN では、オンプレミスのサイトと仮想ネットワークの間にセキュリティで保護された接続を作成することができます。サイト間接続を作成するには、オンプレミスのネットワークに配置されている VPN デバイスを、Azure VPN Gateway を使用したセキュリティで保護された接続を作成するように構成します。接続が作成されると、ローカル ネットワーク上のリソースと仮想ネットワーク内のリソースが直接かつ安全に通信できるようになります。サイト間接続では、仮想ネットワーク内のリソースにアクセスするために、ローカル ネットワーク上のクライアント コンピューターごとに個別の接続を確立する必要はありません。

**サイト間接続を使用する状況:**

- ハイブリッドなソリューションを作成する場合。
- クライアント側の構成を必要としない、オンプレミスの場所と仮想ネットワークの間の接続が必要な場合。
- 永続的な接続が必要な場合。 

**要件**

- オンプレミスの VPN デバイスには、インターネットに接続された IPv4 IP アドレスが必要です。これは、NAT の内側にすることはできません。
- 互換性のある VPN デバイスが必要です。「[VPN デバイスについて](http://go.microsoft.com/fwlink/p/?LinkID=615099)」を参照してください。 
- 使用する VPN デバイスが、ソリューションに必要なゲートウェイの種類と互換する必要があります。「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md)」を参照してください。
- ゲートウェイの SKU は、集計スループットにも影響します。詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gateway-skus)」を参照してください。 

サイト間 VPN Gateway 接続の構成の詳細については、「[サイト間 VPN 接続を使用した仮想ネットワークの構成](vpn-gateway-site-to-site-create.md)」を参照してください。

RRAS を使用してサイト間 VPN Gateway 接続を作成する場合は、「[Windows Server 2012 のルーティングとリモート アクセス サービス (RRAS) を使用した Azure 仮想ネットワークのサイト間 VPN](https://msdn.microsoft.com/library/dn636917.aspx)」を参照してください。


## ポイント対サイト接続

ポイント対サイト VPN では、仮想ネットワークにセキュリティで保護された接続を作成することもできます。ポイント対サイト構成では、仮想ネットワークに接続する各クライアント コンピューターで、接続が個別に構成されます。ポイント対サイト接続では、VPN デバイスは必要ありません。この種類の接続では、各クライアント コンピューターにインストールする VPN クライアントを使用します。VPN を確立するには、オンプレミスのクライアント コンピューターからの接続を手動で開始します。

ポイント対サイトの構成とサイト間の構成は同時に存在することができますが、ポイント対サイト接続は、サイト間接続とは異なり、同じ仮想ネットワークへの ExpressRoute 接続を同時に構成することはできません。

**ポイント対サイト接続を使用する状況:**

- 仮想ネットワークに接続するために構成するクライアントの数があまり多くない。

- リモートの場所から仮想ネットワークに接続する必要がある。たとえば、コーヒー ショップや会議場から接続する場合など。

- サイト間の接続はあるが、リモートの場所から接続する必要があるクライアントが含まれている。

- サイト間接続の最小要件を満たす VPN デバイスにアクセスできない。

- インターネットに接続された IPv4 IP アドレスが VPN デバイスにない。

ポイント対サイト接続の構成の詳細については、「[Azure Virtual Network へのポイント対サイト VPN 接続の構成](vpn-gateway-point-to-site-create.md)」を参照してください。

## ExpressRoute 接続

Azure ExpressRoute は、Azure のデータセンターとオンプレミスや共用環境にあるインフラストラクチャの間でプライベート接続を作成するサービスです。ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

オンプレミスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合もあります。ExpressRoute では、ExpressRoute の場所 (Exchange プロバイダーの施設) にある Azure への接続を確立したり、ネットワーク サービス プロバイダーが提供する既存の WAN ネットワーク (MPLS VPN など) から Azure に直接接続したりできます。

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。


## 次のステップ

「[ExpressRoute の FAQ](../expressroute/expressroute-faqs.md)」と「[VPN Gateway の FAQ](vpn-gateway-vpn-faq.md)」を参照してください。

<!---HONumber=Oct15_HO3-->