<properties
   pageTitle="ExpressRoute の場所 | Microsoft Azure"
   description="この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="cherylmc" />


# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute パートナーとピアリングの場所

この記事の表では、ExpressRoute 接続プロバイダー、ExpressRoute の地理的範囲、ExpressRoute でサポートされる Microsoft クラウド サービス、ExpressRoute システム インテグレーター (SI) に関する情報を提供します。

## <a name="<a-name="partners"></a>expressroute-connectivity-providers"></a><a name="partners"></a>ExpressRoute 接続プロバイダー

ExpressRoute は、すべての Azure のリージョンと場所でサポートされています。 次のマップは Azure のリージョンと ExpressRoute の場所を一覧しています。 ExpressRoute の場所は、Microsoft がいくつかのサービス プロバイダーと接続している場所を示します。

![Location map][0]

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。 次の表は、地理的リージョン、ExpressRoute の場所、Azure リージョンをまとめたものです。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|
|**北米**|米国東部、米国西部、米国東部 2、米国中部、米国中南部、米国中北部、カナダ中部、カナダ東部|アトランタ、シカゴ、ダラス、ラスベガス、ロサンゼルス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC、モントリオール+、ケベックシティ+、トロント|
|**南アメリカ**|ブラジル南部|サンパウロ|
|**ヨーロッパ**|北ヨーロッパ、西ヨーロッパ、英国西部、英国南部|アムステルダム、ダブリン、ロンドン、ニューポート (ウェールズ)+、パリ|
|**アジア**|東アジア、東南アジア|香港、シンガポール|
|**日本**|西日本、東日本|大阪、東京|
|**オーストラリア**|オーストラリア東部、オーストラリア南東部|メルボルン、シドニー|
|**インド**|インド西部、インド中部、インド南部|チェンナイ、ムンバイ|



次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|---|
|**米国政府のクラウド**|米国政府アイオワ、米国政府バージニア|シカゴ、ダラス、ニューヨーク、ワシントン DC|
|**中国**|中国北部、中国東部|北京、上海|
|**ドイツ**|ドイツ中部、ドイツ東部|ベルリン、フランクフルト|


標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。 グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。 国内のクラウド環境には接続できません。 国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。


## <a name="connectivity-provider-locations"></a>接続プロバイダーの場所

> [AZURE.SELECTOR]
[プロバイダーで整理した場所](expressroute-locations.md#connectivity-provider-locations)
[場所で整理したプロバイダー](expressroute-locations-providers.md#connectivity-provider-locations)

### <a name="production-azure"></a>運用 Azure
| **場所**  | **サービス プロバイダー** |
|---------------|-----------------------|
| **アムステルダム** | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT+、InterCloud、Internet Solutions - Cloud Connect、Interxion、Level 3 Communications、Orange、Tata Communications、TeleCity Group、Telenor、Verizon |
| **アトランタ** | Equinix |
| **チェンナイ** | Tata Communications |
| **シカゴ** | AT&T NetBond、Comcast、Equinix、Level 3 Communications、Zayo Group |
| **ダラス** | AT&T NetBond、Cologix、Equinix、Level 3 Communications、Megaport |
| **ダブリン** | Colt、Telecity Group |
| **香港** | British Telecom、China Telecom Global、Equinix、Megaport、Orange、PCCW Global Limited、Tata Communications、Verizon |
| **ロンドン** | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc+、Level 3 Communications、MTN、NTT Communications、Orange、Tata Communications、Telecity Group、Telenor、Verizon、Vodafone |
| **ラスベガス** | Level 3 Communications+、Megaport
| **ロサンゼルス** | CoreSite、Equinix、Megaport、NTT、Zayo Group |
| **メルボルン** | AARNet、Equinix、Megaport、NEXTDC、Telstra Corporation |
| **ニューヨーク** | Equinix、Megaport、Zayo Group |
| **モントリオール** | Cologix+ |
| **ムンバイ** | Tata Communications |
| **大阪** | Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank |
| **パリ** | Interxion |
| **サンパウロ** | Equinix、Telefonica |
| **シアトル** | Equinix、Level 3 Communications、Megaport |
| **シリコン バレー** | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink+、Comcast、Equinix、Level 3 Communications、Orange、Tata Communications、Verizon、Zayo Group |
| **シンガポール** | Aryaka Networks、AT&T NetBond、British Telecom、Equinix、InterCloud、Megaport、Orange、SingTel、Tata Communications、Verizon |
| **シドニー** | AARNet、AT&T NetBond、British Telecom、Equinix、Megaport、NEXTDC、Orange、Telstra Corporation、Verizon |
| **東京** | Aryaka Networks、British Telecom、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、Softbank、Verizon |
| **トロント** | Cologix、Equinix、Zayo Group |
| **ワシントン DC** | Aryaka Networks、AT&T NetBond、British Telecom、Comcast、Equinix、InterCloud、Level 3 Communications、Megaport、Orange、Tata Communications、Verizon、Zayo Group |

 **+** は近日対応予定を表します

### <a name="national-cloud-environments"></a>国内のクラウド環境

#### <a name="us-government-cloud"></a>米国政府のクラウド

| **場所**  |**サービス プロバイダー** |
|---------------|--------------------|
| **シカゴ** | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **ダラス** |  Equinix、Verizon+ |
| **ニューヨーク** | Equinix、Level 3 Communications+、Verizon |
| **ワシントン DC** | AT&T NetBond、Equinix、Level 3 Communications、Verizon |

#### <a name="china"></a>中国

| **場所**  | **サービス プロバイダー** |
|---------------|-----------------------|
| **北京** | China Telecom |
| **上海** |  China Telecom |
詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

#### <a name="germany"></a>ドイツ

| **場所**  | **サービス プロバイダー** |
|---------------|-----------------------|
| **ベルリン** | Colt、e-shelter |
| **フランクフルト** | Colt、Equinix、Interxion |

## <a name="<a-name="nonpartners"></a>connectivity-through-service-providers-not-listed"></a><a name="nonpartners"></a>一覧にないサービス プロバイダー経由の接続

接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

- 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。

    - [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
    - [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
    - [InterXion](http://www.interxion.com/)
    - [NextDC](http://www.nextdc.com/)
    - [CoreSite](http://www.coresite.com/)
    - [Cologix](http://www.cologix.com/)
- その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
    - 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
- Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
    - 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

|**場所**|**Exchange**|**接続プロバイダー**|
|-------------|------------|-------------------------|
| **ニューヨーク** | Equinix | Lightower |
| **シアトル** | Equinix | Alaska Communications |
| **シリコン バレー** | Equinix | XO Communications |
| **シンガポール** | Equinix | 1CLOUDSTAR |
| **ワシントン DC** | Equinix | Lightower |

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター

ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

|**大陸**|**システム インテグレーター**|
|-------------|---------------------|
| **アジア** | Avanade Inc.、OneAs1a|
| **ヨーロッパ** | Avanade Inc.、Dotnet Solutions|
| **米国** | Avanade Inc.、Equinix Professional Services、Perficient、Project Leadership|

## <a name="next-steps"></a>次のステップ

- ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
- すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"



<!--HONumber=Oct16_HO2-->


