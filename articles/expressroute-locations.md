<properties
   pageTitle="ExpressRoute の場所"
   description="このページでは、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。"
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/29/2015"
   ms.author="cherylmc" />

# ExpressRoute パートナーとピアリングの場所
この表に、以下の詳細を示します。

1. ExpressRoute 接続プロバイダー (EXP と NSP)
2. ExpressRoute の地理的範囲
3. ExpressRoute でサポートされる Microsoft クラウド サービス
4. ExpressRoute システム インテグレーター (SI)

## ExpressRoute 接続プロバイダー
ExpressRoute は、すべての Azure のリージョンと場所でサポートされています。次のマップは、Azure のリージョンと ExpressRoute の場所を一覧しています。ExpressRoute の場所は、Microsoft がいくつかのサービス プロバイダーと接続している場所を示します。
 
![](./media/expressroute-locations/expressroute-locations-map.png)

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。次の表は、地理的リージョン内の ExpressRoute の場所に Azure リージョンのマップを提供します。

|**地理的リージョン**|**Azure リージョン**|**ExpressRoute の場所**|
|---|---|---|
|**米国**|米国内のすべてのリージョン - 米国東部、米国西部、米国東部 2、米国中部、米国中南部、米国中北部|アトランタ、シカゴ、ダラス、ロサンゼルス、ニューヨーク、シアトル、シリコン バレー、ワシントン DC|
|**南アメリカ**|ブラジル南部|サンパウロ|
|**ヨーロッパ**|北ヨーロッパ、西ヨーロッパ|アムステルダム、ロンドン|
|**アジア**|東アジア、東南アジア|香港、シンガポール|
|**日本**|西日本、東日本|東京|
|**オーストラリア**|オーストラリア東部、オーストラリア南東部|シドニー|

地理的リージョン間の接続はサポートされていません。接続プロバイダーを利用して、組織のネットワークを使って地理的リージョン間の接続を拡張できます。


### Exchange プロバイダー (EXP) の場所
- Exchange プロバイダーと、そのプロバイダーがサポートされる場所の一覧については、こちらの[表](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_EXP)をご覧ください。
-  接続を設定する手順については、「[EXP 接続の構成](expressroute-configuring-exps.md)」に関するページをご覧ください。

### ネットワーク サービス プロバイダー (NSP) の場所
- ネットワーク サービス プロバイダーと、そのプロバイダーがサポートされる場所の一覧については、こちらの[表](https://msdn.microsoft.com/library/azure/4da69a0f-8f52-49ea-a990-dacd4202150a#BKMK_NSP)をご覧ください。
- 接続を設定する手順については、「[NSP 接続の構成](expressroute-configuring-nsps.md)」に関するページをご覧ください。

### 上記以外のサービス プロバイダー経由の接続

接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

- その接続プロバイダーが、EXP の場所に一覧されているいずれかの Exchange プロバイダーに接続されているかどうかを確認します。次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。いくつかの接続プロバイダーは、既に EXP のイーサネットに接続されています。
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- その接続プロバイダーに、選択した Exchange の場所までネットワークを拡張してもらいます。
	- 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
	- 接続プロバイダー (特に、イーサネット プロバイダー) は、高可用性を確保するために、イーサネット交換に 1 組の回線を調達する必要があります。 
- Azure に接続するには、Exchange プロバイダーを通じて ExpressRoute 回線を注文します。
	- 「[EXP 接続の構成](expressroute-configuring-exps.md)」の手順に従って接続を設定します。

|**接続プロバイダー**|**Exchange プロバイダー**|**ピアリングの場所**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|シリコン バレー|


## ExpressRoute と Microsoft クラウド サービス
次の表は、接続プロバイダーの詳細と、接続プロバイダーがサポートする Microsoft クラウド サービスの一覧を示しています。サービス プロバイダーにお問い合わせください。

**Exchange プロバイダー (EXP)**

|**サービス プロバイダー**|**Microsoft Azure サービス**|**Office 365 サービス**|
|---|---|---|
|**Aryaka**|サポートされています||
|**Colt Ethernet**|サポートされています||
|**Equinix**|サポートされています|近日対応予定|
|**InterCloud**|サポートされています||
|**Level 3 EVPL Service**|サポートされています||
|**TeleCity Group**|サポートされています||
|**Zayo Group**|サポートされています||

**ネットワーク サービス プロバイダー (NSP)**

|**サービス プロバイダー**|**Microsoft Azure サービス**|**Office 365 サービス**|
|---|---|---|
|**AT&T**|サポートされています|近日対応予定|
|**British Telecom**|サポートされています|近日対応予定|
|**Colt IPVPN**|サポートされています||
|**Internet Initiative Japan Inc. - IIJ**|サポートされています||
|**Level3 IPVPN**|サポートされています||
|**Orange**|サポートされています|| 
|**SingTel**|サポートされています||
|**Tata Communications**|サポートされています||
|**Telstra Corporation**|サポートされています||
|**Verizon**|サポートされています|| 


## ExpressRoute システム インテグレーター
ニーズに合わせてプライベート接続を有効にすると、ネットワークの規模によっては難しい場合があります。次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。


|**システム インテグレーター**|**大陸**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|米国||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|ヨーロッパ・中東・アフリカ|

## 次のステップ
- [ExpressRoute の前提条件](expressroute-prerequisites.md)を満たしていることをご確認ください。
- 詳細については、[FAQ](expressroute-faqs.md) をご覧ください。
- プロバイダーを選択して接続を構成します。構成の詳細については、「[EXP 接続の構成](expressroute-configuring-exps.md)」または「[NSP 接続の構成](expressroute-configuring-nsps.md)」に関するページをご覧ください。


<!---HONumber=54-->