---
title: "Azure Government のネットワーク | Microsoft Docs"
description: "Azure Government へのプライベート接続に関する機能比較と指針について取り上げます。"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


---
# <a name="azure-government-networking"></a>Azure Government のネットワーク
## <a name="expressroute-private-connectivity"></a>ExpressRoute (プライベート接続)
ExpressRoute は、Azure Government で一般提供されています。 詳細については (パートナーとピアリングの場所を含む)、[ExpressRoute のパブリック ドキュメント](../expressroute/index.md)をご覧ください。

### <a name="variations"></a>バリエーション
ExpressRoute は、Azure Government で一般提供 (GA) されています。 

* 政府機関のお客様は、専用の Azure Government (Gov) ExpressRoute (ER) 接続を介して、物理的に分離されたキャパシティに接続します。
* Azure Gov は、少なくとも 500 マイル以上離れた場所にある複数のリージョンのペアを活用することで、可用性と持続性の向上を実現します。 
* Azure Gov ER 接続は、既定ではすべてアクティブ-アクティブの冗長構成でバースティング サポートを備えており、最大 10G の回線 (最小 50MB) を提供します。
* Azure Gov ER の配置場所により、お客様と Azure Gov の地理冗長領域にとって最適な経路 (最短のホップ、低待機時間、高パフォーマンスなど) を提供します。
* Azure Gov ER のプライベート接続では、インターネットを使用したりインターネット上を移動することはなく、インターネットに一切依存していません。
* Azure Gov の物理的および論理的なインフラストラクチャは、物理的に分離された専用のインフラストラクチャであり、アクセスは米国の担当者に限定されています。
* Azure Gov のリージョンと Azure Gov ER の Meet-Me の場所間のファイバー インフラストラクチャはすべて Microsoft が所有し、運用を行います。
* Azure Gov ER は、Microsoft Azure、Office 365、および CRM クラウド サービスへの接続を提供します。

### <a name="considerations"></a>考慮事項
Azure Government へのプライベート ネットワーク接続には、VPN (一般的な組織のサイト間接続) を使用する方法と ExpressRoute を使用する方法とがあります。

Azure ExpressRoute は、Azure Government データセンターとオンプレミス インフラストラクチャの間やコロケーション環境内でプライベート接続を確保するために使用されます。 ExpressRoute 接続はパブリック インターネットを経由しないので、一般的なインターネット接続と比べて信頼性が高く、高速で、待ち時間も短くなります。 オンプレミスのシステムと Azure の間のデータ転送に ExpressRoute 接続を使用することで、コスト上の大きなメリットが得られます。   

ExpressRoute では、ExpressRoute の場所 (Exchange プロバイダーの施設など) で Azure への接続を確立することも、ネットワーク サービス プロバイダーが提供する既存の WAN ネットワーク (Multi-Protocol Label Switching (MPLS) VPN など) から Azure へ直接接続することもできます。

![alt text](./media/azure-government-capability-private-connectivity-options.PNG)  ![alt text](./media/government-capability-expressroute.PNG)  

ネットワーク サービスが Azure Government を利用するお客様のアプリケーションとソリューションの足かせにならないように、Azure Government への接続には ExpressRoute (プライベート接続) を導入することを強くお勧めします。 VPN 接続を使用する場合は、次の点を考慮してください。

* プライベート接続やその他の安全な接続機構が必要であるかどうかは、お客様がその運用認可責任者 (機関) に問い合わせて判断する必要があります。また、考慮すべきその他の制限事項があれば、それらについてもお客様が見極める必要があります。
* プライベート接続ゾーンを経由するようにサイト間 VPN をルーティングするよう義務付けるかどうかは、お客様が判断する必要があります。
* ライセンスされたプライベート接続アクセス プロバイダーとの VPN または MPLS 回線はお客様が確保する必要があります。

プライベート接続アーキテクチャを利用するすべてのお客様は、Azure Government の Gateway Network/Internet (GN/I) エッジ ルーターの責任分界点に対するユーザー接続に関して、適切な実装が確立、維持されていることを確認する必要があります。 同様に、Azure Government に使用される Gateway Network/Customer (GN/C) エッジ ルーターの責任分界点とオンプレミス環境との間のネットワーク接続は貴社にて確立してください。

## <a name="next-steps"></a>次のステップ
補足情報と最新情報については、<a href="https://blogs.msdn.microsoft.com/azuregov/">Microsoft Azure Government ブログ</a>を随時ご覧ください。




<!--HONumber=Nov16_HO3-->


