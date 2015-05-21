<properties
   pageTitle="サイト間 VPN の ExpressRoute への移行"
   description="この記事では、サイト間 VPN を ExpressRoute に移行する方法について説明します。"
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
   ms.date="02/23/2015"
   ms.author="cherylmc" />
   
# 接続の移行: サイト間 VPN から ExpressRoute へ

Azure ユーザーは、サイト間 VPN と ExpressRoute の 2 つの方法で仮想ネットワークに接続できます。サイト間 VPN 接続はパブリック インターネット上で行われます。IPsec は、ネットワーク トラフィックの暗号化に使用されます。ExpressRoute は、Azure へのプライベート接続です。[Exchange プロバイダー \(EXP\)](expressroute-exchange-providers.md) または[ネットワーク サービス プロバイダー \(NSP\)](expressroute-network-service-providers.md) のいずれかを経由して Azure に接続できます。

既にサイト間 VPN を使用して仮想ネットワークに接続している場合は、次の手順に従って ExpressRoute 経由の接続に移行できます。

1\) Azure ポータルにログインします。

2\) ナビゲーション ウィンドウで、**\[ネットワーク\]** をクリックし、VPN 接続を持つ仮想ネットワークをクリックします。

3\) **\[ダッシュボード\]** をクリックします。ページの下部にある **\[ゲートウェイの削除\]** クリックし、**\[はい\]** をクリックします。

  この手順は、サイト間 VPN のゲートウェイを削除します。

4\) 仮想ネットワークの "THE GATEWAY WAS NOT CREATED" というメッセージがポータルに表示されたら、**\[構成\]** ページをクリックします。

a\) \[Use ExpressRoute\] チェック ボックスをクリックします。

b\) ゲートウェイ サブネットの CIDR を /28 に変更します。

![サブスクリプションの共有](./media/expressroute-s2s-er-migration/expressroute-s2s-er.png)

5\) ページの下部にある **\[保存\]** クリックし、**\[はい\]** をクリックします。

6\) **\[ダッシュボード\]** をクリックします。ページの下部にある **\[ゲートウェイの作成\]** クリックし、**\[はい\]** をクリックします。

 この手順は、ExpressRoute ゲートウェイを作成します。
 
新しいゲートウェイが作成されると、仮想ネットワークは ExpressRoute 回線に接続する準備ができます。仮想ネットワークのすべての仮想マシンが起動し、プロセス全体で実行しています。仮想マシンを移動したり、シャットダウンしたりする必要はありません。

[Exchange プロバイダー](expressroute-configuring-exps.md) / [ネットワーク サービス プロバイダー](expressroute-configuring-nsps.md)のチュートリアルから ExpressRoute 構成の最後の手順に進み、仮想ネットワークを ExpressRoute 回線にリンクします。



<!--HONumber=54-->