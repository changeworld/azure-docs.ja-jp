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
   ms.date="06/17/2016"
   ms.author="cherylmc" />

# ExpressRoute パートナーとピアリングの場所

この記事の表では、ExpressRoute 接続プロバイダー、ExpressRoute の地理的範囲、ExpressRoute でサポートされる Microsoft クラウド サービス、ExpressRoute システム インテグレーター (SI) に関する情報を提供します。

## <a name="partners"></a>ExpressRoute 接続プロバイダー

ExpressRoute は、すべての Azure のリージョンと場所でサポートされています。次のマップは Azure のリージョンと ExpressRoute の場所を一覧しています。ExpressRoute の場所は、Microsoft がいくつかのサービス プロバイダーと接続している場所を示します。

![Location map][0]

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。次の表は、地理的リージョン、ExpressRoute の場所、Azure リージョンをまとめたものです。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|
|**北米**|米国東部、米国西部、米国東部 2、米国中部、米国中南部、米国中北部、カナダ中部、カナダ東部|アトランタ、シカゴ、ダラス、ラスベガス+、ロサンゼルス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC、モントリオール+、ケベックシティ+、トロント|
|**南アメリカ**|ブラジル南部|サンパウロ|
|**ヨーロッパ**|北ヨーロッパ、西ヨーロッパ|アムステルダム、ダブリン、ロンドン、ニューポート (ウェールズ)+、パリ|
|**アジア**|東アジア、東南アジア|香港、シンガポール|
|**日本**|西日本、東日本|大阪、東京|
|**オーストラリア**|オーストラリア東部、オーストラリア南東部|メルボルン、シドニー|
|**インド**|インド西部、インド中部、インド南部|チェンナイ、ムンバイ|



次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|---|
|**米国政府のクラウド**|米国政府アイオワ、米国政府バージニア|シカゴ、ダラス+、ニューヨーク、ワシントン DC|
|**中国**|中国北部、中国東部|北京、上海|
|**ドイツ**|ドイツ中部、ドイツ東部|ベルリン、フランクフルト|


標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。国内のクラウド環境には接続できません。国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。


## 接続プロバイダーの場所

### 運用 Azure

| **サービス プロバイダー** |**Microsoft Azure** | **Office 365 と CRM Online** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | サポートされています | サポートされています | アムステルダム、シリコン バレー、シンガポール、東京、ワシントン DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | サポートされています | サポートされています | アムステルダム、シカゴ、ダラス、ロンドン、シリコン バレー、シンガポール、シドニー、ワシントン DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | サポートされています | サポートされています | アムステルダム、香港、ロンドン、シリコン バレー、シンガポール、シドニー、東京、ワシントン DC |
|**CenturyLink** | 近日対応予定 | 近日対応予定| シリコン バレー |
|**China Telecom Global** | サポートされています | サポートされていません | 香港 |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | サポートされています | 近日対応予定 | モントリオール+、トロント |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | サポートされています | サポートされています | アムステルダム、ダブリン、ロンドン、東京 |
| **Comcast** | サポートされています | サポートされています | シカゴ、シリコン バレー、ワシントン DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | サポートされています | サポートされています | ロサンゼルス | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | サポートされています | サポートされています | アムステルダム、アトランタ、シカゴ、ダラス、香港、ロンドン、ロサンゼルス、メルボルン、ニューヨーク、大阪、サンパウロ、シアトル、シリコン バレー、シンガポール、シドニー、東京、トロント、ワシントン DC |
| **euNetworks** | サポートされています | サポートされています | アムステルダム |
| **GÉANT** | 近日対応予定 | 近日対応予定 | アムステルダム+ |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** | サポートされています | サポートされています | 大阪、東京 |
| **[InterCloud](https://www.intercloud.com/)** | サポートされています | サポートされています | アムステルダム、ロンドン、シンガポール、ワシントン DC |
| **Internet Solutions - Cloud Connect** | サポートされています | サポートされています | アムステルダム、ロンドン |
| **Interxion** | サポートされています | サポートされています | アムステルダム、ロンドン、パリ |
| **Jisc** | 近日対応予定 | 近日対応予定 | ロンドン+ | 
| **[レベル 3 通信](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | サポートされています | サポートされています | アムステルダム、シカゴ、ダラス、ラスベガス+、ロンドン、シアトル、シリコン バレー、ワシントン DC |
| **Megaport** | サポートされています | サポートされています | ダラス、ラスベガス+、ロサンゼルス、メルボルン、ニューヨーク、シアトル、シンガポール、シドニー、ワシントン DC |
| **MTN** | サポートされています | サポートされています | ロンドン |
| **NEXTDC** | サポートされています | サポートされています | メルボルン、シドニー |
| **NTT Communications** | サポートされています | サポートされています | ロンドン、大阪、東京 |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** | サポートされています | サポートされています | アムステルダム、香港、ロンドン、シリコン バレー、シンガポール、ワシントン DC |
| **PCCW Global Limited** | サポートされています | サポートされています | 香港 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | サポートされています | サポートされています | シンガポール |
| **ソフトバンク** | サポートされています | サポートされています | 大阪、東京 | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | サポートされています | サポートされています | アムステルダム、チェンナイ、香港、ロンドン、ムンバイ、シリコン バレー、シンガポール、ワシントン DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | サポートされています | サポートされています | アムステルダム、ロンドン |
| **Telefonica** | 近日対応予定 | 近日対応予定 | サンパウロ+ |
| **Telenor** | サポートされています | サポートされています | アムステルダム、ロンドン |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | サポートされています | 近日対応予定 | メルボルン、シドニー |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | サポートされています | サポートされています | アムステルダム、香港、ロンドン、シリコン バレー、シンガポール、シドニー、東京、ワシントン DC |
| **Vodafone** | サポートされています | サポートされていません | ロンドン | 
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | サポートされています | サポートされています | シカゴ、ロサンゼルス、ニューヨーク、シリコン バレー、トロント、ワシントン DC |

 **+** は近日対応予定を表します

### 国内のクラウド環境

#### 米国政府のクラウド

| **サービス プロバイダー** |**Microsoft Azure** | **Office 365** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | サポートされています | サポートされています | シカゴ、ワシントン DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | サポートされています | サポートされています | シカゴ、ダラス+、ニューヨーク、ワシントン DC |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | サポートされています | 近日対応予定 | シカゴ、ニューヨーク+、ワシントン DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | サポートされています | サポートされています | シカゴ、ダラス+、ニューヨーク、ワシントン DC |

#### 中国

| **サービス プロバイダー** |**Microsoft Azure** | **Office 365** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | サポートされています | サポートされていません | 北京、上海|
詳細については、[中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/) に関するページを参照してください。

#### ドイツ

| **サービス プロバイダー** |**Microsoft Azure** | **Office 365** | **場所** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | サポートされています | サポートされていません | ベルリン+、フランクフルト|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | 近日対応予定 | サポートされていません | フランクフルト+|
| **e-shelter** | 近日対応予定 | サポートされていません | ベルリン+|
| **Interxion** | サポートされています | サポートされていません | フランクフルト|

## <a name="nonpartners"></a>一覧にないサービス プロバイダー経由の接続

接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

- 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。一部の接続プロバイダーは既にイーサネット Exchange に接続されています。

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [InterXion](http://www.interxion.com/)
	- [NextDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
	- 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
- Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
	- 「[ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)」の手順に従い、接続を構築します。

|**接続プロバイダー**|**Exchange**|**場所**|
|---|---|---|
|**Alaska Communications**|Equinix|シアトル|
|**[XO Communications](http://www.xo.com/)**|Equinix|シリコン バレー|

## ExpressRoute システム インテグレーター

ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

|**システム インテグレーター**|**大陸**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| アジア、ヨーロッパ、米国 |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| ヨーロッパ |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|米国||
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | アジア |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | 米国 |
|**[Project Leadership](http://www.projectleadership.net/azure)** | 米国 |

## 次のステップ

- ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
- すべての前提条件を満たしていることを確認します。「[Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Location map"

<!---HONumber=AcomDC_0622_2016-->