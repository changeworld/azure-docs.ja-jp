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
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# 仮想ネットワークのセキュリティで保護されたクロスプレミス接続について

この記事では、オンプレミスのサイトを Azure の仮想ネットワークに接続するさまざまな方法について説明します。この記事は、Resource Manager とクラシック デプロイ モデルの両方に適用されます。VPN Gateway の接続ダイアグラムをお探しの場合は、「[Azure VPN Gateway 接続トポロジ](vpn-gateway-topology.md)」を参照してください。

サイト間、ポイント対サイト、ExpressRoute という 3 つの接続オプションを使用できます。選択するオプションは、次のようなさまざまな考慮事項によって異なります。


- ソリューションにどのようなスループットが必要か。
- セキュリティで保護された VPN 経由のパブリックなインターネットで通信するか、またはプライベートな接続で通信するか。
- 使用できるパブリック IP アドレスを持っているか。
- VPN デバイスを使用する予定があるか。 その場合、互換性があるか。
- 接続するコンピューターは数台か、サイトとの永続的な接続が必要か。
- 作成するソリューションにはどのような種類の VPN Gateway が必要か。

次の表は、ソリューションに最適な接続オプションを決定するのに役立ちます。

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## サイト間接続

サイト間 VPN では、オンプレミスのサイトと仮想ネットワークの間にセキュリティで保護された接続を作成できます。サイト間接続を作成するには、オンプレミスのネットワークに配置されている VPN デバイスを、Azure VPN Gateway を使用したセキュリティで保護された接続を作成するように構成します。接続が作成されると、ローカル ネットワーク上のリソースと仮想ネットワーク内のリソースが直接かつ安全に通信できるようになります。サイト間接続では、仮想ネットワーク内のリソースにアクセスするために、ローカル ネットワーク上のクライアント コンピューターごとに個別の接続を確立する必要はありません。

**サイト間接続を使用する状況**

- ハイブリッドなソリューションを作成する場合。
- クライアント側の構成を必要としない、オンプレミスの場所と仮想ネットワークの間の接続が必要な場合。
- 永続的な接続が必要な場合。 

**要件**

- オンプレミスの VPN デバイスには、インターネットに接続された IPv4 IP アドレスが必要です。これは、NAT の内側にすることはできません。
- 互換性のある VPN デバイスが必要です。「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。 
- 使用する VPN デバイスが、ソリューションに必要なゲートウェイの種類と互換する必要があります。「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」を参照してください。
- ゲートウェイの SKU は、集計スループットにも影響します。詳細については、「[ゲートウェイの SKU](vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。 

**サイト間接続で使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## ポイント対サイト接続

ポイント対サイト VPN でも、仮想ネットワークへのセキュリティで保護された接続を作成できます。ポイント対サイト構成では、仮想ネットワークに接続するクライアント コンピューターごとに個別に接続を構成します。ポイント対サイト接続では、VPN デバイスは必要ありません。この種類の接続では、各クライアント コンピューターにインストールする VPN クライアントを使用します。VPN を確立するには、オンプレミスのクライアント コンピューターからの接続を手動で開始します。

ポイント対サイトの構成とサイト間の構成は同時に存在することができますが、ポイント対サイト接続は、サイト間接続とは異なり、同じ仮想ネットワークへの ExpressRoute 接続を同時に構成することはできません。

**ポイント対サイト接続を使用する状況**

- 仮想ネットワークに接続するために構成するクライアントの数があまり多くない。

- リモートの場所から仮想ネットワークに接続する必要がある。たとえば、コーヒー ショップや会議場から接続する場合など。

- サイト間接続はあるが、リモートの場所から接続する必要があるクライアントが含まれている。

- サイト間接続の最小要件を満たす VPN デバイスにアクセスできない。

- インターネットに接続された IPv4 IP アドレスが VPN デバイスにない。

**ポイント対サイト接続で使用可能なデプロイ モデルとデプロイ方法**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## ExpressRoute 接続

Azure ExpressRoute は、Azure のデータセンターとオンプレミスや共用環境にあるインフラストラクチャの間でプライベート接続を作成するサービスです。ExpressRoute 接続はパブリックなインターネットを経由しないため、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

オンプレミスと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合もあります。ExpressRoute では、ExpressRoute の場所 (Exchange プロバイダーの施設) にある Azure への接続を確立したり、ネットワーク サービス プロバイダーが提供する既存の WAN ネットワーク (MPLS VPN など) から Azure に直接接続したりできます。

ExpressRoute の詳細については、「[ExpressRoute の技術概要](../expressroute/expressroute-introduction.md)」を参照してください。


## 次のステップ

- VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」、「VPN Gateway に関する [FAQ](vpn-gateway-vpn-faq.md)」、「[VPN ゲートウェイの計画と設計](vpn-gateway-plan-design.md)」の各記事を参照してください。

- ExpressRoute の詳細については、ExpressRoute の[技術概要](../expressroute/expressroute-introduction.md)、[FAQ](../expressroute/expressroute-faqs.md)、[ワークフロー](../expressroute/expressroute-workflows.md)を参照してください。

<!---HONumber=AcomDC_0518_2016-->