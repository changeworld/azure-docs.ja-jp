<properties
   pageTitle="クラシックから Resource Manager への ExpressRoute 回線の移行 | Microsoft Azure"
   description="このページでは、クラシック デプロイ モデルと Resource Manager デプロイ モデルのブリッジについて知っておく必要がある情報を概説します。"
   documentationCenter="na"
   services="expressroute"
   authors="ganesr"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/01/2016"
   ms.author="ganesr"/>

# クラシック デプロイ モデルから Resource Manager デプロイ モデルへの ExpressRoute 回線の移行

この記事では、ExpressRoute 回線をクラシック デプロイ モデルから Resource Manager デプロイ モデルに移行する意味について概説します。

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

単一の ExpressRoute 回線を使用して、クラシック デプロイ モデルにデプロイされた仮想ネットワーク (VNet) と Resource Manager デプロイ モデルにデプロイされた VNet の両方に接続できます。この両方のデプロイ モデルにまたがって、その作成方法を問わず、ExpressRoute 回線を仮想ネットワークにリンクできるようになりました。

![](./media/expressroute-move/expressroute-move-1.png)

## クラシック デプロイ モデルで作成された ExpressRoute 回線

クラシック デプロイ モデルで作成された ExpressRoute 回線の場合、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方に接続できるようにするためには、最初に Resource Manager デプロイ モデルに移行する必要があります。接続の移行中に接続が失われたり中断が発生したりすることはありません。クラシック デプロイ モデルにおける (同じサブスクリプション内およびサブスクリプション間の) 回線と VNet 間のすべてのリンクは保持されます。移行が正常に完了すると、ExpressRoute 回線の見た目、パフォーマンス、操作感は、Resource Manager デプロイ モデルで作成された ExpressRoute 回線とまったく同じになります。これで、Resource Manager デプロイ モデルの仮想ネットワークへの接続を作成することができます。

ExpressRoute 回線を Resource Manager デプロイ モデルに移行すると、ライフ サイクルを管理できる対象が Resource Manager デプロイ モデルを使用している ExpressRoute 回線のみになります。これは、ピアリングの追加、更新、削除、回線のプロパティ (帯域幅、SKU、課金タイプなど) の更新、回線の削除などの操作を行うことができるのが Resource Manager デプロイ モデルに限られることを意味します。両方のデプロイ モデルへのアクセスを管理する方法の詳細については、Resource Manager デプロイ モデルで作成された回線に関する以下のセクションを参照してください。

移行にあたって接続プロバイダーの関与は必要ありません。

## Resource Manager デプロイ モデルで作成された ExpressRoute 回線

Resource Manager デプロイ モデルで作成された ExpressRoute 回線を両方のデプロイ モデルからアクセスできるように設定できます。サブスクリプションのすべての ExpressRoute 回線は、両方のデプロイ モデルからアクセスできるように設定できます。

- Resource Manager デプロイ モデルで作成された ExpressRoute 回線は、既定でクラシック デプロイ モデルにアクセスできません。
- クラシック デプロイ モデルから Resource Manager デプロイ モデルに移行された ExpressRoute 回線は、既定で両方のデプロイ モデルからアクセスできます。
- ExpressRoute 回線は、作成されたのが Resource Manager デプロイ モデルであるかクラシック デプロイ モデルであるかに関係なく、必ず Resource Manager デプロイ モデルにアクセスできます。これは、[仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に従うことで、Resource Manager デプロイ モデルで作成された仮想ネットワークへの接続を作成できることを意味します。 
- クラシック デプロイ モデルへのアクセスは、ExpressRoute 回線の "allowClassicOperations" パラメーターを使用して制御します。 

>[AZURE.IMPORTANT] [サービスの制限](../azure-subscription-service-limits.md)に関するページに記載されているすべてのクォータが適用されます。たとえば、Standard の回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方にわたって最大で 10 個の VNet リンク/接続を持つことができます。


### クラシック デプロイ モデルへのアクセスの制御

ExpressRoute 回線の "allowClassicOperations" パラメーターを設定すると、単一の ExpressRoute 回線で両方のデプロイ モデルの VNet にリンクできるようになります。

"allowClassicOperations" を TRUE に設定すると、両方のデプロイ モデルから ExpressRoute 回線に VNet をリンクできます。クラシック デプロイ モデルの VNet にリンクするには、[クラシック デプロイ モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-classic.md)に関するガイダンスに従ってください。Resource Manager デプロイ モデル モードの VNet にリンクするには、[Resource Manager デプロイ モデルの仮想ネットワークをリンクする方法](expressroute-howto-linkvnet-arm.md)に関するガイダンスに従ってください。

"allowClassicOperations" を FALSE に設定すると、クラシック デプロイ モデルから回線へのアクセスがブロックされます。ただし、クラシック デプロイ モデルのすべての VNet リンクは保持されます。この場合、ExpressRoute 回線は、クラシック デプロイ モデルで認識されません。

### クラシック デプロイ モデルでサポートされる操作

"allowClassicOperations" が TRUE に設定されている場合は、ExpressRoute 回線で次の従来の操作がサポートされます。

 - ExpressRoute 回線の情報の取得
 - クラシック VNet への VNet リンクの作成、更新、取得、削除
 - サブスクリプション間接続用の VNet リンクの承認の作成、更新、取得、削除

"allowClassicOperations" が TRUE に設定されている場合は、次の従来の操作を実行することはできません。

 - Azure プライベート、Azure パブリック、および Microsoft ピアリングのための BGP ピアリングの作成、更新、取得、削除
 - ExpressRoute 回線の削除

## クラシック デプロイ モデルと Resource Manager デプロイ モデルとの間の通信

ExpressRoute 回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの間のブリッジのように動作します。クラシック デプロイ モデルの VNet にデプロイされた仮想マシンと Resource Manager デプロイ モデルの VNet にデプロイされた仮想マシンとの間のトラフィックは、両方の VNet が同じ ExpressRoute 回線にリンクされている場合は ExpressRoute を通過します。合計スループットは、仮想ネットワーク ゲートウェイのスループット容量によって制限されます。トラフィックは、接続プロバイダーのネットワークや組織のネットワークを通過しません。VNet 間のトラフィック フローは、完全に Microsoft ネットワーク内で完結します。

## Azure パブリックおよび Microsoft ピアリング リソースへのアクセス

通常、Azure パブリック ピアリングおよび Microsoft ピアリングからアクセスできるリソースに対して、中断することなくアクセスできます。

## サポートされる操作

このセクションでは、この機能によってサポートされる操作について説明します。

 - 単一の ExpressRoute 回線を使用して、クラシック デプロイ モデルにデプロイされた VNet と Resource Manager デプロイ モデルにデプロイされた VNet にアクセスできます。
 - ExpressRoute 回線をクラシック デプロイ モデルから Resource Manager デプロイ モデルに移行できます。移行が完了すると、ExpressRoute 回線の見た目、操作感、パフォーマンスは、Resource Manager デプロイ モデルで作成された ExpressRoute 回線と同じになります。
 - 移行できるのは ExpressRoute 回線のみです。この操作では、回線リンク、VNet、および VPN ゲートウェイは移行されません。
 - ExpressRoute 回線を Resource Manager デプロイ モデルに移行すると、ライフ サイクルを管理できる対象が Resource Manager デプロイ モデルを使用している ExpressRoute 回線のみになります。これは、ピアリングの追加、更新、削除、回線のプロパティ (帯域幅、SKU、課金タイプなど) の更新、回線の削除などの操作を行うことができるのが Resource Manager デプロイ モデルに限られることを意味します。
 - ExpressRoute 回線は、クラシック デプロイ モデルと Resource Manager デプロイ モデルの間のブリッジのように動作します。クラシック デプロイ モデルの VNet にデプロイされた仮想マシンと Resource Manager デプロイ モデルの VNet にデプロイされた仮想マシンとの間のトラフィックは、両方の VNet が同じ ExpressRoute 回線にリンクされている場合は ExpressRoute を通過します。 
 - クラシック デプロイ モデルと Resource Manager デプロイ モデルの両方におけるサブスクリプション間接続。

## サポートされない操作

このセクションでは、この機能によってサポートされない操作について説明します。

 - クラシック デプロイ モデルから Resource Manager デプロイ モデルへの回線リンク、ゲートウェイ、および仮想ネットワークの移行。
 - クラシック デプロイ モデルからの ExpressRoute 回線のライフサイクルの管理。
 - RBAC のクラシック デプロイ モデルのサポート。クラシック デプロイ モデルで回線に対して RBAC 制御を実行することはできません。サブスクリプションのすべての管理者および共同管理者は、VNet と回線のリンク/リンク解除を行うことができます。

## 構成

「[Moving an ExpressRoute circuit from classic to Resource Manager (クラシックから Resource Manager への ExpressRoute 回線の移行)](expressroute-howto-move-arm.md)」の手順に従ってください。

## 次のステップ

- ワークフロー情報については、「[ExpressRoute 回線のプロビジョニング ワークフローと回線の状態](expressroute-workflows.md)」を参照してください。
- ExpressRoute 接続を構成します。

	- [ExpressRoute 回線の作成](expressroute-howto-circuit-arm.md)
	- [ルーティングの構成](expressroute-howto-routing-arm.md)
	- [ExpressRoute 回線への VNet のリンク](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0406_2016-->