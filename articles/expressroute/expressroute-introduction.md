<properties 
   pageTitle="ExpressRoute の概要 | Microsoft Azure"
   description="このページでは、ExpressRoute 接続のしくみ、Exchange プロバイダーとネットワーク サービス プロバイダー、ExpressRoute のパブリック ピアリング、プライベート ピアリング、Microsoft ピアリングの使用を含む、ExpressRoute サービスの概要について説明します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/15/2015"
   ms.author="cherylmc"/>

# ExpressRoute の技術概要

Microsoft Azure ExpressRoute は、Microsoft のデータセンターとオンプレミスや共用環境にあるインフラストラクチャの間にプライベート接続を作成できるサービスです。ExpressRoute では、Microsoft Azure や Office 365 などの Microsoft クラウド サービスへの接続を、ExpressRoute パートナーの共用施設で確立できます。または、既存の WAN ネットワークから、ネットワーク サービス プロバイダーが提供する MPLS VPN などを使用して直接接続できます。
 
ExpressRoute 接続は、インターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。オンプレミス ネットワークと Azure の間のデータ転送に ExpressRoute 接続を使用することで、大きなコスト上のメリットが得られる場合もあります。オンプレミス ネットワークから Azure までのクロスプレミス接続を作成済みである場合は、仮想ネットワークをそのまま維持しながら ExpressRoute 接続に移行することができます。

詳細については、[ExpressRoute の FAQ](expressroute-faqs.md) を参照してください。

## ExpressRoute 接続のしくみ

既存の WAN を Microsoft クラウド サービスに接続するには、接続プロバイダーに専用回線を注文して有効にしてもらう必要があります。接続プロバイダーおよび回線は、Excange プロバイダー (EXP) を経由する直接レイヤー 3 と、ネットワーク サービス プロバイダー (NSP) を経由するレイヤー 3 の 2 種類あります。WAN と Microsoft クラウドの接続の種類として、どちらかまたは両方を有効にできます。

## Exchange プロバイダーとネットワーク サービス プロバイダー
ExpressRoute のプロバイダーは、ネットワーク サービス プロバイダー (NSP) と Exchange プロバイダー (EXP) に分けられます。

![](./media/expressroute-introduction/expressroute-nsp-exp.png)


| |**Exchange プロバイダー**|**ネットワーク サービス プロバイダー**|
|---|---|---|
|**一般的な接続モデル**| ポイント ツー ポイントのイーサネット リンクまたはクラウド エクスチェンジでの接続 | 電話会社の VPN 経由の任意のシステム間の接続 |
|**サポートされる帯域幅**|200 Mbps、500 Mbps、1 Gbps、10 Gbps|10 Mbps、50 Mbps、100 Mbps、500 Mbps、1 Gbps|
|**接続プロバイダー**|[Exchange プロバイダー](expressroute-locations.md)|[ネットワーク サービス プロバイダー](expressroute-locations.md)|
|**ルーティング**|顧客のエッジ ルーターとの直接の BGP セッション| 電話会社との BGP セッション|
|**料金**|[EXP の料金](http://azure.microsoft.com/pricing/details/expressroute/)|[NSP の料金](http://azure.microsoft.com/pricing/details/expressroute/)|

### Exchange プロバイダー (EXP)
Microsoft では、Equinix や TeleCity グループなどのクラウド エクスチェンジ サービス プロバイダーおよび Cole や Level 3 などのポイント ツー ポイント接続サービス プロバイダーとの提携を通じて、Azure とオンプレミスとの接続を提供しています。回線の帯域幅は、200 Mbps ～ 10 Gbps (200 Mbps、500 Mbps、1 Gbps、および 10 Gbps) を提供します。

Exchange プロバイダー経由の直接レイヤー 3 接続を使用する場合は、次の 3 つのいずれかの方法を使用できます。

- Microsoft がサービスを提供している地域で、Equinix の Cloud Exchange や TeleCity の Cloud IX などのクラウド エクスチェンジと併置してもらう。このケースでは、クラウド エクスチェンジへの冗長接続を注文します。 
- Level 3 などのプロバイダーと提携して、自社のデータセンターと Microsoft との間にイーサネット回線を設定してもらう。 
- 地域の接続プロバイダーと提携して、最も近い Exchange プロバイダーの施設への冗長接続とクラウド エクスチェンジへの接続を取得する。

弊社の SLA の要件を満たしている冗長接続を取得してください。Microsoft のエッジへの直接接続はサポートされません。専用回線は常にイーサネット プロバイダーまたは地域のクラウド エクスチェンジを経由して実現されます。これにより、Microsoft とお客様のネットワークのレイヤー 2 接続が設定されますが、レイヤー 2 ドメインの拡張はサポートされていません。レイヤー 3 接続を確立するには、エッジ ルーターと Microsoft のエッジ ルーターとの間に冗長なルーティング セッションを設定する必要があります。

構成に関する詳細と実際の事例については、「[Exchange プロバイダーを通じて ExpressRoute 接続を構成する](expressroute-configuring-exps.md)」のステップ バイ ステップ ガイダンスを参照してください。


### ネットワーク サービス プロバイダー (NSP)

Microsoft は、AT&T や British Telecom などの電話会社と提携して、Azure とお客様の WAN との接続を提供しています。回線の帯域幅は、10 Mbps ～ 1 Gbps (10 Mbps、50 Mbps、100 Mbps、500 Mbps、および 1 Gbps) を提供します。

Microsoft と提携しているネットワーク サービス プロバイダーのいずれかが提供する VPN サービスを使用すれば、新たにハードウェアをデプロイしたり、既存のネットワークに大きな構成上の変更を加えたりせずに、ネットワークを Azure に拡張できます。

構成に関する詳細と実際の事例については、[ネットワーク サービス プロバイダーによる ExpressRoute 接続の構成](expressroute-configuring-nsps.md)に関するステップ バイ ステップ ガイダンスをご覧ください。

## ExpressRoute のピアリング
下の図は、お客様の WAN と Microsoft の接続を論理的に表現したものです。接続プロバイダー (NSP/EXP) を経由して WAN を Microsoft に接続するには、専用回線を注文する必要があります。専用回線は、接続プロバイダーを経由する WAN と Microsoft の間の論理接続を表しています。複数の専用回線を注文してそれぞれを同じリージョンや異なるリージョンに配置したり、複数のサービス プロバイダーを経由して WAN に接続したりすることができます。

![](./media/expressroute-introduction/expressroute-basic.png)

専用回線には、複数のルーティング ドメイン (パブリック、プライベート、Microsoft) が関連付けられます。各ルーティング ドメインは、高可用性を実現するために、ルーターのペアに同じ方法 (アクティブ/アクティブ構成または負荷分散構成) で構成されます。

![](./media/expressroute-introduction/expressroute-peerings.png)


### プライベート ピアリング
Azure のコンピューティング サービス、つまり仮想ネットワークにデプロイされる Virtual Machines (IaaS) と Cloud Services (PaaS) には、プライベート ピアリング ドメイン経由で接続できます。プライベート ピアリング ドメインは、お客様のコア ネットワークを Microsoft Azure に信頼できる方法で拡張したものと言えます。コア ネットワークと Azure Virtual Network (VNET) の間に双方向接続を設定できます。これにより、Virtual Machines と Cloud Services にプライベート IP アドレスで直接接続できます。

プライベート ピアリング ドメインには複数の仮想ネットワークを接続できます。上限と制限事項については、[FAQ のページ](expressroute-faqs.md)を参照してください。
  

### パブリック ピアリング
Azure Storage、SQL Database、Websites などのサービスは、パブリック IP アドレスで提供されます。パブリック ピアリング ルーティング ドメインを経由して、(クラウド サービスの VIP などの) パブリック IP アドレスでホストされているサービスにプライベート接続できます。パブリック ピアリング ドメインを自社のエクストラネットに接続すれば、インターネット経由で接続しなくても WAN からパブリック IP アドレス上のすべての Azure サービスに接続できます。接続は、常に WAN から Microsoft Azure サービスへ開始されます。このルーティング ドメインを経由して Microsoft Azure サービスからお客様のネットワークに接続を開始することはできません。パブリック ピアリングを有効になると、すべての Azure サービスに接続できるようになります。ルートをアドバタイズするサービスを選択することはできません。このピアリングを経由してアドバタイズされるプレフィックスの一覧については、[Microsoft Azure データセンターの IP 範囲](http://www.microsoft.com/download/details.aspx?id=41653)に関するページを参照してください。ネットワーク内でカスタム ルート フィルターを定義して、必要なルートのみを使用することができます。

パブリック ピアリング ルーティング ドメインでサポートされるサービスの詳細については、[FAQ のページ](expressroute-faqs.md)を参照してください。
 
### Microsoft ピアリング
上記以外の Microsoft Online Services (Office 365 サービスなど) への接続はすべて Microsoft ピアリングを経由します。Microsoft ピアリング ルーティング ドメイン経由で、お客様の WAN と Microsoft クラウド サービスの双方向接続を実現しています。お客様か接続プロバイダーが所有するパブリック IP アドレスのみを使用して Microsoft クラウド サービスに接続する必要があり、Microsoft が定義するすべての規則を遵守する必要があります。詳細については、[ExpressRoute の前提条件](expressroute-prerequisites.md)に関するページを参照してください。

サポートされるサービス、料金、および構成の詳細については、[FAQ のページ](expressroute-faqs.md)を参照してください。Microsoft ピアリングのサポートを提供している接続プロバイダーの一覧については、[ExpressRoute の提供地域](expressroute-locations.md)に関するページを参照してください。


次の表は、3 つのルーティング ドメインを比較しています。

||**プライベート ピアリング**|**パブリック ピアリング**|**Microsoft ピアリング**|
|---|---|---|---|
|**各ピアリングでサポートされるプレフィックスの最大数**|既定 4,000、ExpressRoute Premium 10,000|既定 4,000、ExpressRoute Premium 10,000|200|
|**サポートされる IP アドレス範囲**|お客様の WAN 内の任意の有効な IPv4 アドレス|お客様または接続プロバイダーが所有するパブリック IPv4 アドレス|お客様または接続プロバイダーが所有するパブリック IPv4 アドレス|
|**AS 番号の要件**|プライベートおよびパブリックの AS 番号。お客様はパブリック AS 番号を所有している必要があります。| プライベートおよびパブリックの AS 番号。お客様はパブリック AS 番号を所有している必要があります。| パブリック AS 番号のみ。ルーティング レジストリに対して AS 番号を検証し、所有権を確認する必要があります。|
|**ルーティング インターフェイスの IP アドレス**|RFC1918 およびパブリック IP アドレス|ルーティング レジストリでお客様/NSP に登録されているパブリック IP アドレス。| ルーティング レジストリでお客様/NSP に登録されているパブリック IP アドレス。|
|**MD5 ハッシュのサポート**| あり|あり|あり|

専用回線の一部として 1 つ以上のルーティング ドメインを有効にできます。すべてのルーティング ドメインを 1 つのルーティング ドメインに取り込みたい場合は、同じ VPN (NSP の場合) 上に配置することで実現できます。また、上図に似た別のルーティング ドメイン上に配置することもできます。推奨構成は、プライベート ピアリングをコア ネットワークに直接接続し、パブリック ピアリングと Microsoft ピアリングのリンクをエクストラネットに接続する構成です。
 
3 つのピアリング セッションすべてを使用する場合は、BGP セッションのペアを 3 つ (ピアリングの種類ごとに 1 つのペア) 設定する必要があります。BGP セッションのペアによって、高可用性リンクが実現されます。EXP を経由して接続している場合、ルーティングの構成と管理はお客様に責任があります (EXP がルーティング管理を提供していない場合)。NSP 経由の接続を選択すると、ルーティングの管理を NSP に任せることができます。詳細については、以下の ExpressRoute を設定するワークフローを参照してください。


## 次のステップ

- サービス プロバイダーを検索します。[ExpressRoute のサービス プロバイダーと提供地域](expressroute-locations.md)に関するページをご覧ください。
- ExpressRoute 接続を構成します。手順については、「[ネットワーク サービス プロバイダーによる ExpressRoute 接続の構成](expressroute-configuring-nsps.md)」または「[Exchange プロバイダーを通じて ExpressRoute 接続を構成する](expressroute-configuring-exps.md)」をご覧ください。 

<!----HONumber=July15_HO4-->