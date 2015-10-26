<properties 
   pageTitle="仮想ネットワークでのパブリック IP アドレスの使用方法"
   description="パブリック IP アドレスが使用されるように仮想ネットワークを構成する方法を学習します。"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/04/2015"
   ms.author="telmos" />

# Virtual Network (VNet) でのパブリック IP アドレス空間

パブリック IP アドレス空間を VNet に追加できるようになりました。以前は、VNet に追加できるのは RFC 1918 アドレス ブロック (プライベート空間) のみでした。パブリック IP アドレス範囲を追加すると、VNet 内、相互接続された VNet 内、およびオンプレミスの場所からのみ通信可能なプライベート VNet IP アドレス空間の一部として扱われます。

パブリック IP アドレス空間の追加は、概念的には次のようになります。

![パブリック IP の概念](./media/virtual-networks-public-ip-within-vnet/IC775683.jpg)

## パブリック IP アドレス範囲をどうやって追加するのですか。

プライベート IP アドレス範囲の追加と同じ方法で *netcfg* ファイルを使用するか、ポータルで構成することで、パブリック IP アドレス範囲を追加します。パブリック IP アドレス範囲は、VNet の作成時に追加することも、後で追加することもできます。次の例では、同じ仮想ネットワーク内で構成されたパブリックとプライベートの両方の IP アドレス空間を示しています。

![ポータルでのパブリック IP アドレス](./media/virtual-networks-public-ip-within-vnet/IC775684.png)

## 制限事項はありますか。

許可されていない、いくつかの IP アドレス範囲があります。

- 224\.0.0.0/4 (マルチキャスト)

- 255\.255.255.255/32 (ブロードキャスト)

- 127\.0.0.0/8 (ループバック)

- 169\.254.0.0/16 (リンク ローカル)

- 68\.63.129.16/32 (内部 DNS)

## 次のステップ

[仮想ネットワーク (VNet) のプロパティの管理方法](../virtual-networks-settings)

[Virtual Network (VNet) で使用される DNS サーバーの管理方法](../virtual-networks-manage-dns-in-vnet)

[Virtual Network (VNet) の削除方法](../virtual-networks-delete-vnet)

<!---HONumber=Oct15_HO3-->