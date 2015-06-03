<properties 
   pageTitle="ExpressRoute 導入の前提条件"
   description="このページでは、ExpressRoute 回線を注文する前に満たす必要がある要件を一覧します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn"/> <tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="05/06/2015"
   ms.author="cherylmc"/>


# ExpressRoute の前提条件  

ExpressRoute を使用して Microsoft クラウド サービスに接続する場合は、次の前提条件を満たしていることを確認する必要があります。

## 接続の前提条件

- 有効でアクティブな Microsoft Azure アカウント
- 接続を容易にする必要があるユーザーと、[サポートされている一覧](expressroute-locations.md)のネットワーク サービス プロバイダー (NSP) または Exchange プロバイダー (EXP) との関係。ネットワーク サービス プロバイダーまたは Exchange プロバイダーと既に取引関係がある必要があります。使用するサービスが ExpressRoute と互換性があることを確認する必要があります。 
- 使用しているネットワーク サービス プロバイダーが上記の一覧にない場合でも、Azure への接続を作成できます。 
	- そのネットワーク プロバイダーが、上記のいずれかの Exchange の場所に存在するかどうかを確認します。
	- そのネットワーク プロバイダーに、選択した Exchange の場所までネットワークを拡張してもらいます。
	- Azure に接続するには、Exchange プロバイダーを通じて ExpressRoute 回線を注文します。
- サービス プロバイダーのインフラストラクチャへの接続。次に示す項目のうち、1 つ以上の条件を満たしている必要があります。
	- ネットワーク サービス プロバイダーの VPN ユーザーになっていて、ネットワーク サービス プロバイダーの VPN インフラストラクチャに接続されている 1 つ以上の内部設置型サイトを持っている。VPN サービスが ExpressRoute の要件を満たしているかどうかをネットワーク サービス プロバイダーに確認します。
	- Exchange プロバイダーのデータ センターにインフラストラクチャが併置されている。
	- Exchange プロバイダーのイーサネット インフラストラクチャへのイーサネット接続がある。	
- ルーティング構成のための IP アドレスと AS 番号。 
	- プライベート AS 番号を使用して Azure プライベート ピアリングのルーティング ドメインに接続できます。これを使用する場合は、65000 を超える AS 番号が必要です。AS 番号の詳細については、「[Autonomous System (AS) Numbers (AS 番号)](http://www.iana.org/assignments/as-numbers/as-numbers.xhtml)」をご覧ください。
	- ルートを構成する IP アドレス。A/28 サブネットが必要です。オンプレミスまたは Azure で使用される IP アドレス範囲と重複しないようにする必要があります。
	- Azure パブリック サービスとの BGP セッションを構成する場合は、自身のパブリック AS 番号を使用する必要があります。

## 次のステップ

- ExpressRoute の詳細については、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
- ExpressRoute 接続を構成する方法については、次をご覧ください。 
	- [Configure an ExpressRoute Connection through a Network Service Provider (ネットワーク サービス プロバイダーによる ExpressRoute 接続の構成)](expressroute-configuring-nsps.md)
	- [Configure an ExpressRoute Connection through an Exchange Provider (Exchange プロバイダーによる ExpressRoute 接続の構成)](expressroute-configuring-exps.md)


<!---HONumber=54-->