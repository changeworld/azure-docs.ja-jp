---
title: 'ルートベースの VPN ゲートウェイを作成する: Azure Portal | Microsoft Docs'
description: Azure Portal を使用して、ルートベースの VPN ゲートウェイをすばやく作成します
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2018
ms.author: cherylmc
ms.openlocfilehash: 550f655f6eac5a114636978255578eb3753e0d4b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/06/2018
ms.locfileid: "30918134"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Azure Portal を使用してルートベースの VPN ゲートウェイをすばやく作成する

この記事では、Azure Portal を使用して、ルートベースの VPN ゲートウェイをすばやく作成する方法について説明します。  VPN ゲートウェイが使用されるのは、ご利用のオンプレミスのネットワークへの VPN 接続を作成するときです。 また、VNet への接続に VPN ゲートウェイを使用することもできます。 

この記事の手順では、VNet、サブネット、ゲートウェイ サブネット、およびルートベースの VPN ゲートウェイ (仮想ネットワーク ゲートウェイ) を作成します。 ゲートウェイの作成が完了すると、接続を作成できます。 これらの手順には、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="vnet"></a>仮想ネットワークの作成

1. ブラウザーから [Azure ポータル](http://portal.azure.com) に移動し、Azure アカウントでサインインします。
2. **[リソースの作成]** をクリックします。 **[Marketplace を検索]** フィールドに「仮想ネットワーク」と入力します。 検索結果の一覧から **[仮想ネットワーク]** を探してクリックし、**[仮想ネットワーク]** ページを開きます。
3. [仮想ネットワーク] ページの下の方にある **[デプロイ モデルの選択]** の一覧で、ドロップダウンから **[リソース マネージャー]** が選択されていることを確認し、**[作成]** をクリックします。 **[仮想ネットワークの作成]** ページが開きます。
4. **[仮想ネットワークの作成]** ページで、VNet の設定を構成します。 フィールドへの入力時、入力された文字が有効であれば、赤色の感嘆符が緑色のチェック マークに変わります。 次の値を使用します。

  - **名前**: TestVNet1
  - **アドレス空間**: 10.1.0.0/16
  - **サブスクリプション**: 一覧表示されているサブスクリプションが、使用するものであることを確認します。 ドロップダウンを使用して、サブスクリプションを変更できます。
  - **リソース グループ**: TestRG1
  - **場所**: 米国東部
  - **サブネット**: Frontend
  - **アドレス範囲**: 10.1.0.0/24

  ![[仮想ネットワークの作成] ページ](./media/create-routebased-vpn-gateway-portal/create-virtual-network.png "[仮想ネットワークの作成] ページ")
5. 値の入力後、**[ダッシュボードにピン留めする]** を選択して、ダッシュボードで VNet を簡単に検索できるようにして、**[作成]** をクリックします。 **[作成]** をクリックすると、VNet の進捗状況を反映するタイルがダッシュボードに表示されます。 タイルは、VNet の作成が進むに従って変化します。

## <a name="gwsubnet"></a>ゲートウェイ サブネットの追加

ゲートウェイ サブネットには、仮想ネットワーク ゲートウェイ サービスが使用する予約済み IP アドレスが含まれます。 ゲートウェイ サブネットを作成します。

1. ポータルで、仮想ネットワーク ゲートウェイを作成する仮想ネットワークに移動します。
2. ご自身の仮想ネットワーク ページで、**[サブネット]** をクリックして、**[VNet1 - サブネット]** ページを展開します。
3. **[+ゲートウェイ サブネット]** をクリックして、**[サブネットの追加]** ページを開きます。

  ![ゲートウェイ サブネットの追加](./media/create-routebased-vpn-gateway-portal/add-gateway-subnet.png "ゲートウェイ サブネットの追加")
4. ご自身のサブネットの **[名前]** には、"GatewaySubnet" という必須の値が自動的に入力されます。 自動入力された **[アドレス範囲]** の値は、次の値に合わせて調整してください。

  **アドレス範囲 (CIDR ブロック)**: 10.1.255.0/27

  ![ゲートウェイ サブネットの追加](./media/create-routebased-vpn-gateway-portal/gateway-subnet.png "ゲートウェイ サブネットの追加")
5. ゲートウェイ サブネットを作成するには、ページ下部の **[OK]** をクリックします。

## <a name="gwvalues"></a>ゲートウェイ設定の構成

1. ポータル ページの左側で、**[+ リソースの作成]** をクリックし、検索ボックスに「Virtual Network ゲートウェイ」と入力し、**Enter** キーを押します。 **[結果]** から **[Virtual Network ゲートウェイ]** を探してクリックします。
2. [仮想ネットワーク ゲートウェイ] ページの下部で、**[作成]** をクリックして **[仮想ネットワーク ゲートウェイの作成]** ページを開きます。
3. **[仮想ネットワーク ゲートウェイの作成]** ページで、仮想ネットワーク ゲートウェイの値を指定します。

  - **名前**: Vnet1GW
  - **ゲートウェイの種類**: VPN 
  - **VPN の種類**: ルート ベース
  - **SKU**: VpnGw1
  - **場所**: 米国東部
  - **仮想ネットワーク**: **[仮想ネットワーク]、[仮想ネットワークの選択]** の順にクリックして、**[仮想ネットワークの選択]** ページを開きます。 **[VNet1]** を選択します。

  ![ゲートウェイ設定の構成](./media/create-routebased-vpn-gateway-portal/configure-gateway.png "ゲートウェイ設定の構成")

## <a name="pip"></a>パブリック IP アドレスの作成

VPN ゲートウェイには、動的に割り当てられるパブリック IP アドレスが必要です。 VPN ゲートウェイへの接続を作成するときは、これが、ご自身のオンプレミス デバイスの接続先 IP アドレスです。

1. **[最初の IP 構成] の [ゲートウェイ IP 構成の作成]** を選択し、パブリック IP アドレスを要求します。

  ![最初の IP 構成](./media/create-routebased-vpn-gateway-portal/add-public-ip-address.png "最初の IP 構成")
2. **パブリック IP の選択ページ**で、**[+ 新規作成]** をクリックして、**[パブリック IP アドレスの作成]** ページを開きます。
3. 次の値で設定を構成します。

  - **名前**: **VNet1GWIP**
  - **SKU**: **基本**

  ![パブリック IP の作成](./media/create-routebased-vpn-gateway-portal/public-ip-address-name.png "PIP の作成")
4. ページの下部にある **[OK]** をクリックして、自分の変更を保存します。

## <a name="creategw"></a>VPN ゲートウェイの作成

1. **[仮想ネットワーク ゲートウェイの作成]** ページで設定を確認します。 必要に応じて、値を調整します。

  ![VPN ゲートウェイの作成](./media/create-routebased-vpn-gateway-portal/create-vpn-gateway.png "VPN ゲートウェイの作成")
2. ページの下部にある **[作成]** をクリックします。

**[作成]** をクリックすると、設定が検証され、ダッシュボードに **[Deploying Virtual network gateway]\(仮想ネットワーク ゲートウェイのデプロイ\)** タイルが表示されます。 VPN ゲートウェイには最大 45 分かかることがあります。 完了状態を確認するために、ポータル ページの更新が必要な場合があります。

## <a name="viewgw"></a>VPN ゲートウェイの表示

1. ゲートウェイの作成後、ポータルで VNet1 に移動します。 VPN ゲートウェイは、接続されたデバイスとして [概要] ページに表示されます。

  ![接続されたデバイス](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "接続されたデバイス")

2. デバイスの一覧で **[VNet1GW]** をクリックして詳細情報を表示します。

  ![VPN ゲートウェイの表示](./media/create-routebased-vpn-gateway-portal/view-gateway.png "VPN ゲートウェイの表示")

## <a name="next-steps"></a>次の手順

ゲートウェイの作成が完了したら、自分の仮想ネットワークと別の VNet の間の接続を作成できます。 または、自分の仮想ネットワークとオンプレミスの場所の間の接続を作成します。

> [!div class="nextstepaction"]
> [サイト間接続を作成する](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [ポイント対サイト接続を作成する](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [別の VNet への接続を作成する](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)