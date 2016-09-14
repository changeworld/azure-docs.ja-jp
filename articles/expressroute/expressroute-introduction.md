<properties 
   pageTitle="ExpressRoute の概要 | Microsoft Azure"
   description="このページでは、ExpressRoute 接続が機能するしくみをなど、ExpressRoute サービスの概要を提供します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="06/13/2016"
   ms.author="cherylmc"/>

# ExpressRoute の技術概要

Microsoft Azure ExpressRoute を利用すれば、接続プロバイダーが提供する専用プライベート接続で、オンプレミスのネットワークを Microsoft クラウドに拡張できます。ExpressRoute では、Microsoft Azure、Office 365、CRM Online などの Microsoft クラウド サービスへの接続を確立できます。接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。ExpressRoute 接続では、公共のインターネットを利用できません。それにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も短く、高速です。

![](./media/expressroute-introduction/expressroute-basic.png)

**主な利点:**

- 接続プロバイダーによるオンプレミス ネットワークと Microsoft Cloud 間のレイヤー 3 接続。接続には、任意の環境間 (IP VPN) 接続、ポイント ツー ポイントのイーサネット接続、イーサネット交換による仮想交差接続があります。
- 地政学的領域のすべての地域にわたる Microsoft のクラウド サービスへの接続。
- ExpressRoute プレミアム アドオンを持つすべての領域にわたる Microsoft サービスへのグローバル接続。
- 業界標準プロトコル (BGP) でのネットワークと Microsoft 間の動的ルーティング。
- あらゆるピアリングの場所に冗長性を組み込むという高い信頼性。
- 接続アップタイム [SLA](https://azure.microsoft.com/support/legal/sla/)。
- QoS と、Skype for Business など、特別なアプリケーションのためのサービスの複数のクラス。

詳細については、[ExpressRoute の FAQ](expressroute-faqs.md) を参照してください。

## <a name="howtoconnect"></a>ExpressRoute でネットワークを Microsoft に接続するにはどうすればよいですか。

次の 3 つの方法でオンプレミス ネットワークと Microsoft クラウドの間に接続を構築できます。

### クラウド エクスチェンジで共用施設に配置

クラウドの Exchange がある施設に同一場所配置している場合、併置プロバイダーのイーサネット交換経由で Microsoft クラウドに仮想交差接続を要請できます。併置プロバイダーは、共有施設のインフラストラクチャと Microsoft クラウドの間に、レイヤー 2 交差接続と管理レイヤー 3 交差接続のいずれかを提供します。

### ポイント ツー ポイントのイーサネット接続 

オンプレミス データセンター/オフィスと Microsoft クラウドをポイント ツー ポイントのイーサネット リンクで接続できます。ポイント ツー ポイントのイーサネットのプロバイダーは、サイトと Microsoft クラウドの間にレイヤー 2 接続と管理レイヤー 3 接続のいずれかを提供できます。

### 任意の環境間 (IPVPN) ネットワーク

WAN と Microsoft クラウドを統合できます。IPVPN プロバイダー (通常、MPLS VPN) は、ブランチ オフィスとデータセンターの間に任意の環境間の接続を提供できます。Microsoft クラウドを WAN に相互接続し、ブランチ オフィスのように見せることができます。通常、WAN プロバイダーは管理レイヤー 3 接続を提供します。ExpressRoute の機能は上記の接続モデルのすべてに共通しています。

接続プロバイダーでは、1 つ以上の接続モデルを提供できます。最適なモデルは接続プロバイダーに相談の上でお選びください。

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)



## ExpressRoute の機能

ExpressRoute は次のような機能をサポートしています。

### レイヤー 3 接続

Microsoft は業界標準の動的ルーティング プロトコル (BGP) を利用し、オンプレミス ネットワーク、Azure のインスタンス、および Microsoft パブリック アドレスの間のルートを交換します。さまざまなトラフィック プロファイルに合わせ、ネットワークとさまざまな BGP セッションを確立します。詳しくは、[ExpressRoute の回線とルーティング ドメイン](expressroute-circuit-peerings.md)に関する記事を参照してください。

### 冗長性

各 ExpressRoute 回線は、接続プロバイダー/ネットワーク エッジから 2 つの Microsoft Enterprise エッジ ルーター (WSEE) への 2 つの接続で構成されます。Microsoft は接続プロバイダー/あなたの側から 2 つの BGP 接続を必要とします。MSEE につき 1 つです。あなたの側で冗長デバイス/イーサネット回線をデプロイしないことを選択できます。ただし、接続プロバイダーは冗長デバイスを利用することで冗長性が与えられる方法で接続を Microsoft に渡します。冗長レイヤー 3 接続構成は [SLA](https://azure.microsoft.com/support/legal/sla/) を有効にするための必須条件です。

### Microsoft クラウド サービスへの接続

[AZURE.INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

ExpressRoute 接続によって、次のようなサービスにアクセスできます。

- Microsoft Azure サービス
- Microsoft Office 365 サービス
- Microsoft CRM Online サービス
 
[ExpressRoute FAQ](expressroute-faqs.md) ページには、ExpressRoute でサポートされているサービスの詳しい一覧があります。

### 地政学的な地域内のすべての地域への接続

[ピアリングの場所](expressroute-locations.md)の 1 つで Microsoft に接続し、地政学的な領域内のすべての地域にアクセスできます。

たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、北ヨーロッパと西ヨーロッパでホストされているすべての Microsoft クラウド サービスにアクセスできます。地政学的領域、関連付けられている Microsoft 領域、対応する ExpressRoute のピアリングの場所の概要については、「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」という記事を参照してください。

### ExpressRoute プレミアム アドオンによるグローバル接続

ExpressRoute プレミアム アドオン機能を有効にすると、地政学的境界を越えて接続を拡張できます。たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、世界中のあらゆる領域でホストされているすべての Microsoft クラウド サービスにアクセスできます (国内のクラウドは除外します)。北ヨーロッパ地域と西ヨーロッパ地域にアクセスする場合と同じ方法で南アメリカやオーストラリアにデプロイされているサービスにアクセスできます。

### 大勢の接続パートナーが属するエコシステム

ExpressRoute のエコシステムには継続的に新しい接続パートナーと SI パートナーが加わり、拡大します。最新の情報については、[ExpressRoute のプロバイダーと場所](expressroute-locations.md)に関する記事を参照してください。

### 国内クラウドへの接続

Microsoft は特殊な地政学的領域と顧客区分のために隔離されたクラウド環境を運営しています。国内のクラウドとプロバイダーの一覧については、[ExpressRoute のプロバイダーと場所](expressroute-locations.md)に関するページを参照してください。

### サポートされる帯域幅のオプション

さまざまな帯域幅に合った ExpressRoute 回線を購入できます。サポートされる帯域幅の一覧は次のとおりです。サポートされる帯域幅の一覧は接続パートナーに必ず確認してください。

- 50 Mbps
- 100 Mbps
- 200 Mbps
- 500 Mbps
- 1 Gbps
- 2 Gbps
- 5 Gbps
- 10 Gbps

### 帯域幅の動的スケーリング

接続を壊さずに (ベスト エフォート基準で) ExpressRoute 回線の帯域幅を増やすことができます。

### 柔軟な課金モデル

最適な請求モデルを選択できます。次に示す請求モデルから選択します。詳細については、[ExpressRoute FAQ](expressroute-faqs.md) ページを参照してください。

- **無制限のデータ**。ExpressRoute 回線は月額料金に基づいて課金されます。すべての受信または送信データ転送が無料で含まれます。
- **従量制課金データ**。ExpressRoute 回線は月額料金に基づいて課金されます。すべての受信データ転送は無料で含まれます。送信データ転送は 1 GB のデータ転送単位で課金されます。データ転送料金は地域によって異なります。
- **ExpressRoute プレミアム アドオン**。ExpressRoute プレミアムは ExpressRoute 回線のアドオンです。ExpressRoute プレミアム アドオンには次の機能があります。
	- Azure パブリックと Azure プライベートのピアリングのルート上限の増加 (4,000 ルートから 10,000 ルートに)。
	- サービスのグローバル接続。(国内のクラウドを除く) あらゆる地域で作成された ExpressRoute 回線に与えられる世界中の他の地域にあるリソースへのアクセス。たとえば、西ヨーロッパで作成された仮想ネットワークにシリコン バレーでプロビジョニングされた ExpressRoute 回線からアクセスできます。
	- ExpressRoute ごとの VNet リンクの増加 (回線の帯域幅にもよりますが、10 からそれ以上に)。

## 次のステップ

- ExpressRoute 接続とルーティング ドメインについて説明します。「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。
- サービス プロバイダーを検索します。「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。
- すべての前提条件を満たしていることを確認します。「[Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
- [ルーティング](expressroute-routing.md)、[NAT](expressroute-nat.md)、[QoS](expressroute-qos.md) の要件を参照してください。
- ExpressRoute 接続を構成します。
	- [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md)
	- [ルーティングの構成](expressroute-howto-routing-classic.md)
	- [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-classic.md)

<!---HONumber=AcomDC_0907_2016-->