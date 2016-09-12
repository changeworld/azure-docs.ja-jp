<properties
	pageTitle="Azure Government のドキュメント | Microsoft Azure"
	description="Azure Government へのプライベート接続に関する機能比較と指針について取り上げます。"
	services="Azure-Government"
	cloud="gov" 
	documentationCenter=""
	authors="ryansoc"
	manager="zakramer"
	editor=""/>

<tags
	ms.service="multiple"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="azure-government"
	ms.date="08/25/2016"
	ms.author="ryansoc"/>

#  Azure Government のネットワーク

##  ExpressRoute (プライベート接続)

Azure Government へのプライベート ネットワーク接続には、VPN (一般的な組織のサイト間接続) を使用する方法と ExpressRoute を使用する方法とがあります。

Azure ExpressRoute は、Azure Government データセンターとオンプレミス インフラストラクチャの間やコロケーション環境内でプライベート接続を確保するために使用されます。ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。オンプレミスのシステムと Azure の間のデータ転送に ExpressRoute 接続を使用することで、コスト上の大きなメリットが得られます。

ExpressRoute では、ExpressRoute の場所 (Exchange プロバイダーの施設など) で Azure への接続を確立することも、ネットワーク サービス プロバイダーが提供する既存の WAN ネットワーク (Multi-Protocol Label Switching (MPLS) VPN など) から Azure へ直接接続することもできます。

![alt text](./media/azure-government-capability-private-connectivity-options.PNG) ![alt text](./media/government-capability-expressroute.PNG)

ネットワーク サービスが Azure Government を利用するお客様のアプリケーションとソリューションの足かせにならないように、Azure Government への接続には ExpressRoute (プライベート接続) を導入することを強くお勧めします。VPN 接続を使用する場合は、次の点を考慮してください。

• プライベート接続やその他の安全な接続機構が必要であるかどうかは、お客様がその運用認可責任者 (機関) に問い合わせて判断する必要があります。また、考慮すべきその他の制限事項があれば、それらについてもお客様が見極める必要があります。

• プライベート接続ゾーンを経由するようにサイト間 VPN をルーティングするよう義務付けるかどうかは、お客様が判断する必要があります。

• ライセンスされたプライベート接続アクセス プロバイダーとの VPN または MPLS 回線はお客様が確保する必要があります。

プライベート接続アーキテクチャを利用するすべてのお客様は、Azure Government の Gateway Network/Internet (GN/I) エッジ ルーターの責任分界点に対するユーザー接続に関して、適切な実装が確立、維持されていることを確認する必要があります。同様に、Azure Government に使用される Gateway Network/Customer (GN/C) エッジ ルーターの責任分界点とオンプレミス環境との間のネットワーク接続は貴社にて確立してください。

ExpressRoute は、Azure Government で一般提供されています。詳細については (パートナーとピアリングの場所を含む)、<a href="https://azure.microsoft.com/documentation/services/expressroute/">ExpressRoute のパブリック ドキュメント</a>を参照してください。

補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。

<!---HONumber=AcomDC_0831_2016-->