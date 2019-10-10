---
title: Azure VMware Solution by CloudSimple - ネットワーク チェックリスト
description: Azure VMware Solution by CloudSimple でネットワーク CIDR を割り当てるためのチェックリスト
author: sharaths-cs
ms.author: dikamath
ms.date: 09/25/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c31942b198697ddc913f3732fd41409334ff0ef6
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817394"
---
# <a name="networking-prerequisites-for-azure-vmware-solution-by-cloudsimple"></a>Azure VMware Solution by CloudSimple のネットワーク前提条件

Azure VMware Solution by CloudSimple から与えられる VMware Private Cloud 環境には、オンプレミス環境、企業が管理するデバイス、Azure リソースからユーザーやアプリケーションがアクセスできます。 接続は、VPN 接続や ExpressRoute 接続などのネットワーク サービスから与えられます。  一部のネットワーク サービスでは、サービスを有効にする目的でネットワーク アドレス範囲を指定する必要があります。  この記事の表には、一連のアドレス範囲とそれに対応し、指定のアドレスを使用するサービスがまとめてあります。  アドレス指定は必須の場合と配備するサービスに依存する場合があります。  アドレス空間は、オンプレミス サブネット、Azure 仮想ネットワーク サブネット、予定されている CloudSimple ワークロード サブネットのいずれとも重複しないようにしてください。

## <a name="network-address-ranges-required-for-creating-a-private-cloud"></a>プライベート クラウドを作成するために必要なネットワーク アドレス範囲

CloudSimple サービスとプライベート クラウドの作成時、次のネットワーク CIDR 範囲が必要です。

| 名前/使用目的     | 説明                                                                                                                            | アドレス範囲            |
|-------------------|----------------------------------------------------------------------------------------------------------------------------------------|--------------------------|
| ゲートウェイ CIDR      | エッジ サービスに必須 (VPN ゲートウェイ)。  この CIDR は CloudSimple サービスの作成時に必須であり、RFC 1918 空間からのものにする必要があります。 | /28                      |
| vSphere/vSAN CIDR | VMware 管理ネットワークに必須。 この CIDR はプライベート クラウドの作成時に指定する必要があります。                                    | /24 または /23 または /22 または /21 |

## <a name="network-address-range-required-for-azure-network-connection-to-on-premises-network"></a>オンプレミス ネットワークへの Azure ネットワーク接続に必要なネットワーク アドレス範囲。

[ExpressRoute を利用し、プライベート クラウド ネットワークにオンプレミス ネットワーク](on-premises-connection.md)から接続することで、Global Reach 接続が確立されます。  この接続では、オンプレミス ネットワーク、プライベート クラウド ネットワーク、Azure ネットワークの間で BGP を介して経路が交換されます。

| 名前/使用目的             | 説明                                                                                                                                                                             | アドレス範囲 |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------|
| ExpressRoute ピアリング CIDR | オンプレミス接続に ExpressRoute Global Reach が使用されるときに必須。 この CIDR は、サポート チケットを介して Global Reach 接続要求が行われるときに指定する必要があります。 | /29           |

## <a name="network-address-range-required-for-using-site-to-site-vpn-connection-to-on-premises-network"></a>オンプレミス ネットワークへのサイト間 VPN 接続の使用に必要なネットワーク アドレス範囲。

[サイト間 VPN を使用し、プライベート クラウド ネットワークにオンプレミス ネットワーク](vpn-gateway.md)から接続するには、次の IP アドレス、オンプレミス ネットワーク、ID が必要です。 

| アドレス/アドレス範囲 | 説明                                                                                                                                                                                                                                                           |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ピア IP               | オンプレミス VPN ゲートウェイ パブリック IP アドレス。 オンプレミス データセンターと CloudSimple Service リージョンの間でサイト間 VPN を確立するために必須。 この IP はサイト間 VPN ゲートウェイを作成するときに必要です。                                         |
| ピア識別子       | オンプレミス VPN ゲートウェイのピア識別子。 これは通常、**ピア IP** と同じです。  オンプレミス VPN ゲートウェイに一意の識別子が指定されている場合、その識別子を指定する必要があります。  ピア ID はサイト間 VPN ゲートウェイを作成するときに必要です。   |
| オンプレミス ネットワーク   | リージョンで CloudSimple へのアクセスを必要とするオンプレミス プレフィックス。  ユーザーのアクセス元となるクライアント ネットワークなど、CloudSimple ネットワークにアクセスするオンプレミス ネットワークからのすべてのプレフィックスが含まれます。                                         |

## <a name="network-address-range-required-for-using-point-to-site-vpn-connections"></a>ポイント対サイト VPN 接続の使用に必要なネットワーク アドレス範囲

ポイント対サイト VPN 接続では、クライアント コンピューターから CloudSimple ネットワークにアクセスできます。  [ポイント対サイト VPN を設定する](vpn-gateway.md)には、次のネットワーク アドレス範囲を指定する必要があります。

| アドレス/アドレス範囲 | 説明                                                                                                                                                                                                                                                                                                  |
|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| クライアント サブネット         | ポイント対サイト VPN で接続するとき、クライアント サブネットから DHCP アドレスが与えられます。 このサブネットは、CloudSimple ポータルでポイント対サイト VPN ゲートウェイを作成するときに必要になります。  ネットワークは 2 つのサブネットに分割され、1 つは UDP 接続に使用され、もう 1 つは TCP 接続に使用されます。 |

## <a name="next-steps"></a>次の手順

* [クイックスタート - CloudSimple サービスを作成する](quickstart-create-cloudsimple-service.md)
* [quickstart-create-private-cloud](quickstart-create-private-cloud.md)
* Azure ネットワーク接続の詳細については[こちら](cloudsimple-azure-network-connection.md)をご覧ください
* VPN ゲートウェイの詳細については[こちら](cloudsimple-vpn-gateways.md)をご覧ください
