<properties 
   pageTitle="Virtual Network クロスプレミス接続の VPN Gateway について | Microsoft Azure"
   description="ハイブリッド構成のクロスプレミス接続で使用できる VPN ゲートウェイについて説明します。この記事では、ゲートウェイの SKU (Basic、Standard、および 高性能)、VPN Gateway と ExpressRoute の共存構成、ゲートウェイ ルーティングの種類 (静的、動的、ポリシー ベース、ルート ベース)、および仮想ネットワーク接続でのゲートウェイの要件 (クラシック デプロイ モデルとリソース マネージャーのデプロイ モデルの両方向け) について説明します。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# VPN ゲートウェイについて

VPN Gateway は、仮想ネットワークとオンプレミスの場所の間でネットワーク トラフィックの送信に使用されます。また、Azure 内で複数の仮想ネットワーク間のトラフィック送信にも使用されます (VNet 間)。ゲートウェイを作成するときには、いくつかの要因を考慮する必要があります。

- 使用するゲートウェイの SKU
- 接続用の VPN の種類
- VPN デバイス (接続に必要な場合)

**デプロイ モデルについて**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## ゲートウェイの SKU

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## VPN Gateway の種類

次の 2 つの種類の VPN があります。

- **ポリシー ベース:** ポリシー ベースのゲートウェイは、従来のデプロイ モデルでは*静的ゲートウェイ*と呼ばれます。名前は変更されましたが、静的ゲートウェイの機能は変更されていません。この種類のゲートウェイでは、ポリシー ベースの VPN がサポートされています。ポリシー ベースの VPN では、オンプレミスのネットワークと Azure VNet の間のアドレス プレフィックスの組み合わせに基づくトラフィック セレクターにより、IPsec トンネルを経由してパケットが転送されます。トラフィック セレクターまたはポリシーは、通常、VPN の構成でアクセス リストとして定義されます。
 
- **ルート ベース:** ルート ベースのゲートウェイは、従来のデプロイ モデルでは*動的ゲートウェイ*と呼ばれます。名前は変更されましたが、動的ゲートウェイの機能は変更されていません。ルート ベースのゲートウェイは、ルート ベースの VPN を実装します。ルート ベースの VPN は、IP 転送テーブルまたはルーティング テーブルの「ルート」を使用して、対応する VPN トンネル インターフェイスにパケットを転送します。その後、トンネル インターフェイスではトンネルの内部または外部でパケットを暗号化または復号します。ルート ベースの VPN に向けたポリシーまたはトラフィック セレクターは任意の環境間 (またはワイルドカード) として構成されます。

一部の接続 (ポイント対サイト、VNet 間など) は、特定の種類の VPN のみに対応します。作成する接続シナリオに対応する記事のゲートウェイの要件を確認してください。

VPN デバイスには、構成上の制限事項もあります。VPN ゲートウェイを作成するときには、接続に必要な VPN の種類を選択し、使用する VPN デバイスがそのルーティングの種類もサポートしていることを確認します。詳細については、「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。

たとえば、ポイント対サイト接続と同時にサイト間接続を使用する場合は、ルート ベースの VPN ゲートウェイを構成する必要があります。サイト間接続はポリシー ベースのゲートウェイで動作しますが、ポイント対サイト接続には、ルート ベースのゲートウェイの種類が必要です。両方の接続で同じゲートウェイが使用されるため、両方をサポートするゲートウェイの種類を選択する必要があります。さらに、使用する VPN デバイスは、ルート ベースの構成もサポートする必要があります。


## ゲートウェイの要件


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## 次のステップ

VPN デバイスの構成を選択します。「[VPN デバイスについて](vpn-gateway-about-vpn-devices.md)」を参照してください。





 

<!---HONumber=AcomDC_0302_2016-->