---
title: "仮想ネットワーク ゲートウェイの削除: Azure Portal: Resource Manager | Microsoft ドキュメント"
description: "Resource Manager デプロイメント モデルで、PowerShell を使用して仮想ネットワーク ゲートウェイを削除します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: b4949db62ccd31cf6ce3d19df5459367cac99b59
ms.lasthandoff: 03/31/2017


---
# <a name="delete-a-virtual-network-gateway-using-the-portal"></a>ポータルを使用して仮想ネットワーク ゲートウェイを削除する
> [!div class="op_single_selector"]
> * [Resource Manager - Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [クラシック - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

VPN ゲートウェイ構成の仮想ネットワーク ゲートウェイを削除する際に利用できる方法はいくつかあります。

- テスト環境の場合のように、すべてを削除してやり直す場合は、リソース グループを削除することができます。 リソース グループを削除すると、グループ内のすべてのリソースが削除されます。 この方法は、リソース グループ内のどのリソースも保持する必要がない場合にのみお勧めします。 この方法を使用して一部のリソースだけを選択して削除することはできません。

- リソース グループ内の一部のリソースを保持する必要がある場合は、仮想ネットワーク ゲートウェイの削除が少し複雑になります。 仮想ネットワーク ゲートウェイを削除する前に、まず、そのゲートウェイに依存しているリソースをすべて削除する必要があります。 従う手順は、作成した接続の種類と、各接続に依存するリソースによって異なります。

##<a name="deletegw"></a>VPN ゲートウェイの削除

仮想ネットワーク ゲートウェイを削除するには、まず、仮想ネットワーク ゲートウェイに関連する各リソースを削除する必要があります。 リソースは、依存関係に応じた特定の順序で削除する必要があります。

###<a name="step-1-navigate-to-the-virtual-network-gateway"></a>ステップ 1: 仮想ネットワーク ゲートウェイへの移動

[Azure Portal ](https://portal.azure.com)の **[すべてのリソース]** で、削除する仮想ネットワーク ゲートウェイをクリックします。 次にゲートウェイの図を示します。

![ローカルの仮想ネットワーク ゲートウェイ](./media/vpn-gateway-delete-vnet-gateway-portal/gw.png)

###<a name="step-2-delete-connections"></a>ステップ 2: 接続の削除

1. 仮想ネットワーク ゲートウェイのブレードで、**[接続]** をクリックしてゲートウェイへのすべての接続を表示します。
2. 接続名の行で **[...]** をクリックして、ドロップダウンから **[削除]** を選択します。
3. 確認画面で **[はい]** をクリックして、接続を削除します。 複数の接続がある場合は、接続を一つずつ削除します。

###<a name="step-3-delete-the-local-network-gateways"></a>ステップ 3: ローカル ネットワーク ゲートウェイの削除
1. **[すべてのリソース]** で、各接続に関連付けられているローカル ネットワーク ゲートウェイを検索します。 次にローカル ネットワーク ゲートウェイの図を示します。

    ![ローカル ネットワーク ゲートウェイの検索](./media/vpn-gateway-delete-vnet-gateway-portal/lng.png)
2. ローカル ネットワーク ゲートウェイの **[概要]** ブレードで、**[削除]** をクリックします。

###<a name="step-4-delete-the-virtual-network-gateway"></a>ステップ 4: 仮想ネットワーク ゲートウェイの削除
1. **[すべてのリソース]** で、削除する仮想ネットワーク ゲートウェイを検索します。
2. **[概要]** ブレードで、**[削除]** をクリックしてゲートウェイを削除します。

>[!NOTE]
> S2S 構成に加えてこの VNet に対する P2S 構成がある場合は、仮想ネットワーク ゲートウェイを削除すると、警告なしにすべての P2S クライアントが自動的に切断されます。
>
>

###<a name="step-5-delete-the-public-ip-address-for-the-gateway"></a>ステップ 5: ゲートウェイのパブリック IP アドレスの削除

1. **[すべてのリソース]** で、ゲートウェイに割り当て済みのパブリック IP アドレスを検索します。 仮想ネットワーク ゲートウェイがアクティブ/アクティブであった場合は、2 つのパブリック IP アドレスが表示されます。 次にパブリック IP アドレスの図を示します。

    ![パブリック IP アドレス](./media/vpn-gateway-delete-vnet-gateway-portal/pip.png)

2. パブリック IP アドレスの **[概要]** ページで **[削除]** をクリックしてから、**[はい]** をクリックして確認します。

###<a name="step-6-delete-the-gateway-subnet"></a>ステップ 6: ゲートウェイ サブネットの削除

1. **[すべてのリソース]** で、仮想ネットワークを検索します。 
2. **[サブネット]** ブレードで、**[GatewaySubnet]**、**[削除]** の順にクリックします。 
3. 確認画面で **[はい]** をクリックして、ゲートウェイ サブネットを削除します。

##<a name="deleterg"></a>リソース グループを削除して VPN ゲートウェイを削除する

リソース グループにどのリソースも保持する必要がなく、単に最初からやり直したい場合は、リソース グループ全体を削除できます。 すべてを削除するには、これが簡単な方法です。 次の手順は、Resource Manager デプロイ モデルに該当します。

1. **[すべてのリソース]** でリソース グループを見つけ、クリックしてブレードを開きます。
2. [ **削除**] をクリックします。 [削除] ブレードに関連するリソースを表示します。 これらのリソースをすべて削除することを確認します。 削除しない場合は、この記事の上部にある、「[VPN ゲートウェイの削除](#deletegw)」のステップに従ってください。
3. 続行するには、削除するリソース グループの名前を入力し、**[削除]** をクリックします。
