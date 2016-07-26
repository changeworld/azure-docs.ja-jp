<properties
   pageTitle="Azure ポータルを使用して ExpressRoute 回線のルーティングを構成する方法 | Microsoft Azure"
   description="この記事では、ExpressRoute 回線のプライベート、パブリックおよび Microsoft ピアリングを作成し、プロビジョニングする手順について説明します。この記事では、回線のピアリングの状態確認、更新、または削除の方法も示します。"
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc"/>

# ExpressRoute 回線のルーティングの作成と変更を行う



> [AZURE.SELECTOR]
[Azure Portal - Resource Manager](expressroute-howto-routing-portal-resource-manager.md)
[PowerShell - Resource Manager](expressroute-howto-routing-arm.md)
[PowerShell - Classic](expressroute-howto-routing-classic.md)



この記事では、Azure ポータルと Resource Manager のデプロイ モデルを使用して、ExpressRoute 回線のルーティング構成を作成して管理する手順について説明します。

**Azure のデプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## 構成の前提条件

- 構成を開始する前に、必ず、[前提条件](expressroute-prerequisites.md)ページ、[ルーティングの要件](expressroute-routing.md)ページおよび[ワークフロー](expressroute-workflows.md) ページを確認してください。
- アクティブな ExpressRoute 回線が必要です。手順に従って、[ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md)し、接続プロバイダー経由で回線を有効にしてから続行してください。ExpressRoute 回線をプロビジョニングされ、有効になっている状態にする必要があります。そうすれば、以下で説明されているコマンドレットを実行できます。

次の手順は、サービス プロバイダーが提供するレイヤー 2 接続サービスで作成された回線にのみ適用されます。サービス プロバイダーが提供する管理対象レイヤー 3 サービス (MPLS など、通常は IPVPN) を使用する場合、接続プロバイダーがユーザーに代わってルーティングを構成および管理します。


>[AZURE.IMPORTANT] 現在のところ、サービス管理ポータルでは、サービス プロバイダーが構成したピアリングをアドバタイズしていません。できるだけ早くこの機能を提供できるように取り組んでいます。BGP ピアリングを構成する前に、ご利用のサービス プロバイダーにお問い合わせください。

ExpressRoute 回線用に 1 つ、2 つ、または 3 つすべてのピアリング (Azure プライベート、Azure パブリックおよび Microsoft) を構成することができます。ピアリングは任意の順序で構成することができます。ただし、各ピアリングの構成は必ず一度に 1 つずつ完了するようにしてください。

## Azure プライベート ピアリング

このセクションでは、ExpressRoute 回線用の Azure プライベート ピアリング構成を作成、取得、更新、および削除する方法について説明します。

### Azure プライベート ピアリングを作成するには

1. ExpressRoute 回線を構成します。続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。

	![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. 回線用に Azure プライベート ピアリングを構成します。次の手順に進む前に、以下のものがそろっていることを確認します。

	- プライマリ リンク用の /30 サブネット。これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
	- セカンダリ リンク用の /30 サブネット。これを、仮想ネットワーク用に予約されたアドレス空間の一部にすることはできません。
	- このピアリングを確立するための有効な VLAN ID。回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
	- ピアリングの AS 番号。2 バイトと 4 バイトの AS 番号の両方を使用することができます。このピアリングではプライベート AS 番号を使用できます。65515 を使用しないようにしてください。
	- いずれかを使用する場合は、MD5 ハッシュ。**これは省略可能です**。


3. 次のように、Azure プライベート ピアリング行を選択します。
	
	![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
	

4. プライベート ピアリングを構成します。次の図は構成例です。

	![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

	
5. すべてのパラメーターを指定したら、構成を保存します。構成が正常に保存されると、次のような画面が表示されます。

	![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)
	

### Azure プライベート ピアリングの詳細を表示するには

ピアリングを選択して、Azure プライベート ピアリングのプロパティを表示することができます。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)


### Azure プライベート ピアリングの構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### Azure プライベート ピアリングを削除するには

ピアリングの構成を削除するには、次のように削除アイコンを選択します。

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)


## Azure パブリック ピアリング

このセクションでは、ExpressRoute 回線用の Azure パブリック ピアリング構成を作成、取得、更新および削除する方法について説明します。

### Azure パブリック ピアリングを作成するには

1. ExpressRoute 回線を構成します。続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。

	![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. 回線用に Azure パブリック ピアリングを構成します。次の手順に進む前に、以下のものがそろっていることを確認します。

	- プライマリ リンク用の /30 サブネット。
	- セカンダリ リンク用の /30 サブネット。
	- このピアリングをセットアップするために使用するすべての IP アドレスは、有効なパブリック IPv4 アドレスである必要があります。
	- このピアリングを確立するための有効な VLAN ID。回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
	- ピアリングの AS 番号。2 バイトと 4 バイトの AS 番号の両方を使用することができます。
	- いずれかを使用する場合は、MD5 ハッシュ。**これは省略可能です**。

3. 次のように、Azure パブリック ピアリング行を選択します。
	
	![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
	

4. パブリック ピアリングを構成します。次の図は構成例です。

	![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

	
5. すべてのパラメーターを指定したら、構成を保存します。構成が正常に保存されると、次のような画面が表示されます。

	![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)
	

### Azure パブリック ピアリングの詳細を表示するには

ピアリングを選択して、Azure パブリック ピアリングのプロパティを表示することができます。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)


### Azure パブリック ピアリング構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### Azure パブリック ピアリングを削除するには

ピアリングの構成を削除するには、次のように削除アイコンを選択します。

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)


## Microsoft ピアリング

このセクションでは、ExpressRoute 回線の Microsoft ピアリング構成を作成、取得、更新および削除する方法について説明します。

### Microsoft ピアリングを作成するには

1. ExpressRoute 回線を構成します。続行する前に、接続プロバイダーが回線を完全にプロビジョニングしていることを確認します。

	![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)


2. 回路の Microsoft ピアリングを構成する続行する前に、次の情報を確認してください。

	- プライマリ リンク用の /30 サブネット。これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
	- セカンダリ リンク用の /30 サブネット。これは、自分が所有しており、RIR/IRR に登録されている有効なパブリック IPv4 プレフィックスである必要があります。
	- このピアリングを確立するための有効な VLAN ID。回線の他のピアリングが同じ VLAN ID を使用しないようにしてください。
	- ピアリングの AS 番号。2 バイトと 4 バイトの AS 番号の両方を使用することができます。
	- **アドバタイズされたプレフィックス:** BGP セッションを介してアドバタイズする予定のすべてのプレフィックスのリストを指定する必要があります。パブリック IP アドレス プレフィックスのみが受け入れられます。一連のプレフィックスを送信する予定の場合は、コンマ区切りのリストを送信できます。これらのプレフィックスは、RIR/IRR に登録する必要があります。
	- **顧客 ASN:** ピアリング AS 番号に登録されていないプレフィックスをアドバタイズする場合は、そのプレフィックスが登録されている AS 数を指定できます。**これは省略可能です**。
	- **ルーティング レジストリ名:** AS 番号とプレフィックスを登録する RIR/IRR を指定することができます。**これは省略可能です。**
	- いずれかを使用する場合は、MD5 ハッシュ。**これは省略可能です。**
	
3. 次のように、構成するピアリングを選択できます。Microsoft ピアリング行を選択します。
	
	![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
	

4.  Microsoft ピアリングを構成します。次の図は構成例です。

	![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)

	
5. すべてのパラメーターを指定したら、構成を保存します。

	回線が検証が必要な状態になった場合 (次の図を参照)、サポート チケットを開き、プレフィックスの所有権を示す証拠をサポート チームに示してください。
	
	![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)


	次のように、ポータルから直接サポート チケットを開くことができます。
	
	![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


6. 構成が正常に保存されると、次のような画面が表示されます。

	![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)
	

### Microsoft ピアリングの詳細を表示するには

ピアリングを選択して、Azure パブリック ピアリングのプロパティを表示することができます。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)


### Microsoft ピアリング構成を更新するには

ピアリングの行を選択し、ピアリングのプロパティを変更できます。


![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)


### Microsoft ピアリングを削除するには

ピアリングの構成を削除するには、次のように削除アイコンを選択します。

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)



## 次のステップ

次の手順では、[ExpressRoute 回線に VNet をリンク](expressroute-howto-linkvnet-arm.md)します。

-  ExpressRoute ワークフローの詳細については、「[ExpressRoute ワークフロー](expressroute-workflows.md)」を参照してください。

-  回路ピアリングの詳細については、「[ExpressRoute 回線とルーティング ドメイン](expressroute-circuit-peerings.md)」を参照してください。

-  仮想ネットワークの詳細については、「[仮想ネットワークの概要](../virtual-network/virtual-networks-overview.md)」を参照してください。

<!---HONumber=AcomDC_0720_2016-->