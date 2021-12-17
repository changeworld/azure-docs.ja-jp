---
title: Azure NetApp Files のネットワーク計画のガイドライン | Microsoft Docs
description: Azure NetApp Files を使用して、有効なネットワークアーキテクチャを設計するために役立つガイドラインについて説明します。
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/04/2021
ms.author: ramakk
ms.openlocfilehash: 09f09b5c50dd04f39f95405df9875d458a258b25
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545965"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Azure NetApp Files のネットワーク計画のガイドライン

ネットワーク アーキテクチャの計画は、あらゆるアプリケーション インフラストラクチャ設計の重要な要素です。 この記事は、お客様のワークロードに有効なネットワーク アーキテクチャを設計し、Azure NetApp Files の豊富な機能からベネフィットを得るために役立ちます。

Azure NetApp Files のボリュームは、Azure Virtual Network 内の[委任されたサブネット](../virtual-network/virtual-network-manage-subnet.md)と呼ばれる特別な目的のサブネットに含まれるように設計されています。 そのため、必要に応じて、VNet ピアリング経由で Azure 内から直接、またはオンプレミスから仮想ネットワーク ゲートウェイ (ExpressRoute または VPN Gateway) 経由で、ボリュームにアクセスできます。 サブネットは Azure NetApp Files 専用であり、インターネットへの接続はありません。 

## <a name="configurable-network-features"></a>構成可能なネットワーク機能  

 Azure NetApp Files 用の [**Standard ネットワーク機能**](configure-network-features.md)の構成は、パブリック プレビューで利用できます。 お使いのサブスクリプションでこの機能に登録した後は、サポートされているリージョンの *Standard* または *Basic* ネットワーク機能を選択して、新しいボリュームを作成することができます。 Standard ネットワーク機能がサポートされていないリージョンでは、ボリュームは既定で Basic ネットワーク機能を使用します。  

* ***Standard***  
    この設定を選択すると、より高い IP 制限と、[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md)や[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined)などの標準 VNet 機能が委任されたサブネット上で有効になり、この記事で説明されているように追加の接続パターンも有効になります。

* ***Basic***  
    この設定を選択すると、「[考慮事項](#considerations)」セクションで説明されているように、選択的な接続パターンと制限付き IP スケールが有効になります。 この設定では、すべての[制約](#constraints)が適用されます。 

## <a name="considerations"></a>考慮事項  

Azure NetApp Files ネットワークを計画するときは、いくつかの考慮事項を理解している必要があります。

### <a name="constraints"></a>制約

次の表では、各ネットワーク機能構成でサポートされるものについて説明します。

|      特徴     |      Standard ネットワーク機能     |      Basic ネットワーク機能     |
|---|---|---|
|     Azure NetApp Files の VNet で使用される IP の数 (すぐにピアリングされる VNet を含む)    |     [VM としての Standard の制限](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits)    |     1000    |
|     VNet ごとの ANF の委任されたサブネット    |     1    |     1    |
|     Azure NetApp Files の委任されたサブネット上の[ネットワーク セキュリティ グループ](../virtual-network/network-security-groups-overview.md) (NSG)    |     はい    |     いいえ    |
|     Azure NetApp Files の委任されたサブネット上の[ユーザー定義ルート](../virtual-network/virtual-networks-udr-overview.md#user-defined) (UDR)    |     はい    |     いいえ    |
|     [プライベート エンドポイント](../private-link/private-endpoint-overview.md)への接続    |     いいえ    |     いいえ    |
|     [サービス エンドポイント](../virtual-network/virtual-network-service-endpoints-overview.md)への接続    |     いいえ    |     いいえ    |
|     Azure NetApp Files インターフェイス上の Azure ポリシー (カスタム名前付けポリシーなど)    |     いいえ    |     いいえ    |
|     Azure NetApp Files トラフィック用のロード バランサー    |     いいえ    |     いいえ    |
|     デュアル スタック (IPv4 と IPv6) VNet    |     いいえ <br> (IPv4 のみサポート)    |     いいえ <br> (IPv4 のみサポート)    |

### <a name="supported-network-topologies"></a>サポートされているネットワーク トポロジ

次の表では、Azure NetApp Files の各ネットワーク機能構成によってサポートされているネットワーク トポロジについて説明します。 

|      トポロジ     |      Standard ネットワーク機能     |      Basic ネットワーク機能     |
|---|---|---|
|     ローカル VNet 内のボリュームへの接続    |     はい    |     はい    |
|     ピアリングされた VNet 内のボリュームへの接続 (同じリージョン)    |     はい    |     はい    |
|     ピアリングされた VNet 内のボリュームへの接続 (クロス リージョンまたはグローバル ピアリング)    |     いいえ    |     いいえ    |
|     ExpressRoute ゲートウェイ経由でのボリュームへの接続    |     はい    |     はい    |
|     ExpressRoute (ER) FastPath    |     はい    |     いいえ    |
|     ExpressRoute ゲートウェイおよびゲートウェイ トランジットとの VNet ピアリングを経由した、オンプレミスからスポーク VNet 内のボリュームへの接続    |     はい    |     はい    |
|     VPN ゲートウェイを経由した、オンプレミスからスポーク VNet 内のボリュームへの接続    |     はい    |     はい    |
|     VPN ゲートウェイおよびゲートウェイ トランジットとの VNet ピアリングを経由した、オンプレミスからスポーク VNet 内のボリュームへの接続    |     はい    |     はい    |
|     アクティブ/パッシブ VPN ゲートウェイを経由した接続    |     はい    |     はい    |
|     アクティブ/アクティブ VPN ゲートウェイを経由した接続    |     はい    |     いいえ    |
|     アクティブ/アクティブ ゾーン冗長ゲートウェイを経由した接続    |     いいえ    |     いいえ    |
|     Virtual WAN (VWAN) を経由した接続    |     いいえ    |     いいえ    |

## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files ボリューム用の仮想ネットワーク

このセクションでは、仮想ネットワークの計画に役立つ概念について説明します。

### <a name="azure-virtual-networks"></a>Azure 仮想ネットワーク

Azure NetApp Files ボリュームをプロビジョニングする前に、Azure 仮想ネットワーク (VNet) を作成するか、サブスクリプションに既に存在するものを使用する必要があります。 VNet はボリュームのネットワーク境界を定義します。  仮想ネットワークの作成について詳しくは、[Azure Virtual Network のドキュメント](../virtual-network/virtual-networks-overview.md)を参照してください。

### <a name="subnets"></a>サブネット

サブネットは、仮想ネットワークを個別のアドレス空間に分割し、それらの中にある Azure リソースから使用できるようにするものです。  Azure NetApp Files のボリュームは、[委任されたサブネット](../virtual-network/virtual-network-manage-subnet.md)と呼ばれる特別な目的のサブネットに含まれています。 

サブネットの委任では、サブネットにサービス固有のリソースを作成するための明示的なアクセス許可が Azure NetApp Files サービスに与えられます。  サービスのデプロイには一意識別子を使用します。 この場合、Azure NetApp Files への接続を可能にするためにネットワーク インターフェイスが作成されます。

新しい VNet を使用する場合は、「[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)」の手順に従って、サブネットを作成し、そのサブネットを Azure NetApp Files に委任することができます。 他のサービスにまだ委任されていない、既存の空のサブネットを委任することもできます。

VNet が別の VNet とピアリングされている場合、VNet アドレス空間を拡張できません。 そのため、新しい委任されたサブネットは VNet アドレス空間内に作成する必要があります。 アドレス空間を拡張する必要がある場合、アドレス空間を拡張する前に VNet ピアリングを削除する必要があります。

### <a name="udrs-and-nsgs"></a>UDR と NSG

ユーザー定義ルート (UDR) とネットワーク セキュリティ グループ (NSG) は、Standard ネットワーク機能で少なくとも 1 つのボリュームが作成されている、Azure NetApp Files の委任されたサブネットでのみサポートされます。  

> [!NOTE]
> ネットワーク インターフェイス レベルでの NSG の関連付けは、Azure NetApp Files のネットワーク インターフェイスではサポートされていません。 

サブネットに Standard と Basic のネットワーク機能を使用するボリュームの組み合わせがある場合 (または、機能プレビューに登録されていない既存のボリュームの場合)、委任されたサブネットで適用される UDR と NSG は、Standard ネットワーク機能を持つボリュームのみに適用されます。

委任されたサブネットのアドレス プレフィックスと NVA としてのネクスト ホップを使用した、ソース VM サブネットでのユーザー定義ルート (UDR) の構成は、Basic ネットワーク機能を使用するボリュームではサポートされていません。 そのような設定を行うと、接続の問題が発生します。

## <a name="azure-native-environments"></a>Azure ネイティブ環境

次の図は、Azure ネイティブ環境を示しています。

![Azure ネイティブ ネットワーク環境](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>ローカル VNet

基本的なシナリオでは、同じ VNet 内の仮想マシン (VM) から Azure NetApp Files ボリュームを作成するか、それに接続します。 上図の VNet 2 では、ボリューム 1 は委任されたサブネットに作成され、既定のサブネットの VM 1 にマウントすることができます。

### <a name="vnet-peering"></a>VNET ピアリング

互いのリソースへのアクセスを必要とする追加の VNet が同じリージョンにある場合、[VNet ピアリング](../virtual-network/virtual-network-peering-overview.md)を使用して VNet を接続することで、Azure インフラストラクチャを介した安全な接続を実現できます。 

上図の VNet 2 と VNet 3 について考えます。 VM 1 が VM 2 とボリューム 2 に接続する必要がある場合、または VM 2 が VM 1 またはボリューム 1 に接続する必要がある場合は、VNet 2 と VNet 3 の間で VNet ピアリングを有効にする必要があります。 

さらに、同じリージョンで VNet 1 が VNet 2 とピアリングされており、VNet 2 が VNet 3 とピアリングされているシナリオを考えます。 VNet 1 のリソースは VNet 2 のリソースに接続できますが、VNet 1 と VNet 3 がピアリングされていない限り、VNet 3 のリソースには接続できません。 

上の図で、VM 3 はボリューム 1 に接続できますが、VM 4 はボリューム 2 に接続できません。  この理由は、スポーク VNet がピアリングされておらず、_トランジット ルーティングが VNet ピアリング経由ではサポートされない_ からです。

## <a name="hybrid-environments"></a>ハイブリッド環境

次の図は、ハイブリッド環境を示しています。 

![ハイブリッド ネットワーク環境](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

ハイブリッド シナリオでは、オンプレミスのデータセンターのアプリケーションが Azure のリソースにアクセスする必要があります。  これは、データセンターを Azure に拡張したいのか、Azure ネイティブ サービスを使用したいのか、それともディザスター リカバリーが目的なのかに関係なく当てはまります。 サイト間 VPN または ExpressRoute 経由でオンプレミスの複数のリソースを Azure のリソースに接続する方法については、[VPN Gateway の計画オプション](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)に関するページを参照してください。

ハイブリッド ハブスポーク トポロジでは、Azure のハブ VNet は、オンプレミス ネットワークへの主要な接続ポイントとして機能します。 スポークはハブとピアリングされる VNet であり、ワークロードを分離するために使用できます。

構成によっては、オンプレミスのリソースを、ハブおよびスポーク内のリソースに接続できます。

上に示したトポロジでは、オンプレミス ネットワークは Azure のハブ VNet に接続されており、ハブ VNet とピアリングされているのと同じリージョンに 2 つのスポーク VNet があります。  このシナリオで、Azure NetApp Files ボリュームに対してサポートされている接続オプションは次のとおりです。

* オンプレミス リソース VM 1 および VM 2 は、サイト間 VPN または ExpressRoute 回線経由でハブのボリューム 1 に接続できます。 
* オンプレミス リソース VM 1 と VM 2 は、サイト間 VPN とリージョンの VNet ピアリング経由で、ボリューム 2 またはボリューム 3 に接続できます。
* ハブ VNet 内の VM 3 は、スポーク VNet 1 内のボリューム 2 と、スポーク VNet 2 内のボリューム 3 に接続できます。
* スポーク VNet 1 の VM 4 と、スポーク VNet 2 の VM 5 は、ハブ VNet 内のボリューム 1 に接続できます。
* スポーク VNet 1 内の VM 4 は、スポーク VNet 2 内のボリューム 3 に接続できません。 また、スポーク VNet 2 内の VM 5 は、スポーク VNet 1 内のボリューム 2 に接続できません。 これは、スポーク VNet がピアリングされておらず、_トランジット ルーティングは VNet ピアリング経由ではサポートされていない_ からです。
* 上記のアーキテクチャでは、スポーク VNet にもゲートウェイがある場合、ハブのゲートウェイを介して接続しているオンプレミスからの ANF ボリュームへの接続が失われます。 仕様により、スポーク VNet のゲートウェイが優先されるため、そのゲートウェイ経由で接続するコンピューターのみが ANF ボリュームに接続できます。

## <a name="next-steps"></a>次のステップ

* [サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)
* [Azure NetApp Files ボリュームのネットワーク機能を構成する](configure-network-features.md) 
