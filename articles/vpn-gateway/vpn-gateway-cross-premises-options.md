<properties 
   pageTitle="仮想ネットワークのセキュリティで保護されたクロスプレミス接続について | Microsoft Azure"
   description="サイト間、ポイント対サイト、ExpressRoute 接続などの仮想ネットワークのセキュリティで保護されたクロスプレミス接続の種類について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/08/2016"
   ms.author="cherylmc" />

# 仮想ネットワークのセキュリティで保護されたクロスプレミス接続について

この記事では、オンプレミスのサイトを Azure の仮想ネットワークに接続するさまざまな方法について説明します。この記事は、Resource Manager とクラシック デプロイ モデルの両方に適用されます。

サイト間、ポイント対サイト、ExpressRoute という 3 つの接続オプションを使用できます。選択するオプションは、次のようなさまざまな考慮事項によって異なります。


- ソリューションにどのようなスループットが必要か。
- セキュリティで保護された VPN 経由のパブリックなインターネットで通信するか、またはプライベートな接続で通信するか。
- 使用できるパブリック IP アドレスを持っているか。
- VPN デバイスを使用する予定があるか。 その場合、互換性があるか。
- 接続するコンピューターは数台か、サイトとの永続的な接続が必要か。
- 作成するソリューションにはどのような種類の VPN Gateway が必要か。

次の表は、ソリューションに最適な接続オプションを決定するのに役立ちます。


| - | **ポイント対サイト** | **サイト対サイト** | **ExpressRoute** |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Azure でサポートされるサービス** | Cloud Services および Virtual Machines | Cloud Services および Virtual Machines | [サービス一覧](../expressroute/expressroute-faqs.md#supported-services) |
| **一般的な帯域幅** | 一般的には 100 Mbps 未満のアグリゲート | 一般的には 100 Mbps 未満のアグリゲート | 50 Mbps、100 Mbps、200 Mbps、500 Mbps、1 Gbps、2 Gbps、5 Gbps、10 Gbps |
| **サポート対象プロトコル** | Secure Sockets Tunneling Protocol (SSTP) | IPsec | VLAN、NSP の VPN テクノロジー (MPLS、VPLS など) 経由の直接接続 |
| **ルーティング** | ルート ベース (動的) | ポリシー ベース (静的ルーティング) およびルート ベース (動的ルーティング VPN) がサポートされます。 | BGP |
| **接続の弾力性** | アクティブ/パッシブ | アクティブ/パッシブ | アクティブ/アクティブ |
| **一般的な使用例** | プロトタイプの作成、クラウド サービスおよび仮想マシンの開発/テスト/ラボ シナリオ | クラウド サービスおよび仮想マシンの開発/テスト/ラボ シナリオおよび小規模実稼動ワークロード | すべての Azure サービス (検証済み一覧)、エンタープライズクラスおよびミッション クリティカルなワークロード、バックアップ、ビッグ データ、DR サイトとしての Azure へのアクセス |
| **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) |
| **料金** | [料金](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [料金](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [料金](https://azure.microsoft.com/pricing/details/expressroute/) |
| **テクニカル ドキュメント** | [VPN Gateway のドキュメント](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [VPN Gateway のドキュメント](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [ExpressRoute のドキュメント](https://azure.microsoft.com/documentation/services/expressroute/) |
| **FAQ** | [VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md) | [VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md) | [ExpressRoute の FAQ](../expressroute/expressroute-faqs.md) |


## サイト間接続

サイト間 VPN では、オンプレミスのサイトと仮想ネットワークの間にセキュリティで保護された接続を作成できます。サイト間接続を作成するには、オンプレミスのネットワークに配置されている VPN デバイスを、Azure VPN Gateway を使用したセキュリティで保護された接続を作成するように構成します。接続が作成されると、ローカル ネットワーク上のリソースと仮想ネットワーク内のリソースが直接かつ安全に通信できるようになります。サイト間接続では、仮想ネットワーク内のリソースにアクセスするために、ローカル ネットワーク上のクライアント コンピューターごとに個別の接続を確立する必要はありません。

**サイト間接続を使用する状況**

- ハイブリッドなソリューションを作成する場合。
- クライアント側の構成を必要としない、オンプレミスの場所と仮想ネットワークの間の接続が必要な場合。
- 永続的な接続が必要な場合。 

**要件**

- オンプレミスの VPN デバイスには、インターネットに接続された IPv4 IP アドレスが必要です。これは、NAT の内側にすることはできません。
- 互換性のある VPN デバイスが必要です。「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 
- 使用する VPN デバイスが、ソリューションに必要なゲートウェイの種類と互換する必要があります。「[VPN ゲートウェイについて](vpn-gateway-about-vpngateways.md)」を参照してください。
- ゲートウェイの SKU は、集計スループットにも影響します。詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gateway-skus)」を参照してください。 

Azure クラシック ポータルおよびクラシック デプロイ モデルを使用したサイト間 VPN Gateway 接続の構成の詳細については、「[Azure クラシック ポータルでサイト間 VPN 接続を使用して仮想ネットワークを作成する](vpn-gateway-site-to-site-create.md)」を参照してください。Resource Manager デプロイ モデルを使用したサイト間 VPN の構成の詳細については、「[PowerShell を使用してサイト間 VPN 接続で仮想ネットワークを作成する](vpn-gateway-create-site-to-site-rm-powershell.md)」を参照してください。


## ポイント対サイト接続

ポイント対サイト VPN でも、仮想ネットワークへのセキュリティで保護された接続を作成できます。ポイント対サイト構成では、仮想ネットワークに接続するクライアント コンピューターごとに個別に接続を構成します。ポイント対サイト接続では、VPN デバイスは必要ありません。この種類の接続では、各クライアント コンピューターにインストールする VPN クライアントを使用します。VPN を確立するには、オンプレミスのクライアント コンピューターからの接続を手動で開始します。

ポイント対サイトの構成とサイト間の構成は同時に存在することができますが、ポイント対サイト接続は、サイト間接続とは異なり、同じ仮想ネットワークへの ExpressRoute 接続を同時に構成することはできません。

**ポイント対サイト接続を使用する状況**

- 仮想ネットワークに接続するために構成するクライアントの数があまり多くない。

- リモートの場所から仮想ネットワークに接続する必要がある。たとえば、コーヒー ショップや会議場から接続する場合など。

- サイト間接続はあるが、リモートの場所から接続する必要があるクライアントが含まれている。

- サイト間接続の最小要件を満たす VPN デバイスにアクセスできない。

- インターネットに接続された IPv4 IP アドレスが VPN デバイスにない。

クラシック デプロイ モデルのポイント対サイト接続の構成の詳細については、「[VNet へのポイント対サイト VPN 接続の構成](vpn-gateway-point-to-site-create.md)」を参照してください。Resource Manager デプロイ モデルのポイント対サイト接続の構成の詳細については、「[PowerShell を利用し、仮想ネットワークへのポイント対サイト接続を構成する](vpn-gateway-howto-point-to-site-rm-ps.md)」を参照してください。

## ExpressRoute 接続

Azure ExpressRoute は、Azure のデータセンターとオンプレミスや共用環境にあるインフラストラクチャの間でプライベート接続を作成するサービスです。ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

オンプレミスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合もあります。ExpressRoute では、ExpressRoute の場所 (Exchange プロバイダーの施設) にある Azure への接続を確立したり、ネットワーク サービス プロバイダーが提供する既存の WAN ネットワーク (MPLS VPN など) から Azure に直接接続したりできます。

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。


## 次のステップ

詳細については、「[VPN Gateway に関する FAQ](vpn-gateway-vpn-faq.md)」と「[ExpressRoute の FAQ](../expressroute/expressroute-faqs.md)」を参照してください。

<!---HONumber=AcomDC_0316_2016-->