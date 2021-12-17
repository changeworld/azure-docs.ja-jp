---
title: 'アクティブ/アクティブな VPN ゲートウェイの構成: Azure portal'
titleSuffix: Azure VPN Gateway
description: Azure portal を使用してアクティブ/アクティブな仮想ネットワーク ゲートウェイを構成する方法について説明します。
services: vpn-gateway
author: JackStromberg
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/22/2021
ms.author: jstrom
ms.openlocfilehash: 1a5ca58b9e56826c7173c499b64ebabc80ddf11e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746179"
---
# <a name="configure-active-active-vpn-gateways-using-the-portal"></a>portal を使用してアクティブ/アクティブな VPN ゲートウェイを構成する

この記事では、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)と Azure portal を使用して、高可用性のアクティブ/アクティブ VPN ゲートウェイを作成する方法について説明します。 [PowerShell](vpn-gateway-activeactive-rm-powershell.md) を使用してアクティブ/アクティブなゲートウェイを構成することもできます。

クロスプレミスと VNet 間接続で高可用性を実現するには、複数の VPN ゲートウェイをデプロイし、ネットワークと Azure 間に複数の並列接続を確立する必要があります。 接続オプションとトロポジの概要については、「[高可用性のクロスプレミス接続および VNet 間接続](vpn-gateway-highlyavailable.md)」をご覧ください。

> [!IMPORTANT]
> アクティブ/アクティブ モードは、Basic または Standard 以外のすべての SKU で使用できます。 詳細については、[構成設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)に関するページを参照してください。
>

この記事の手順では、VPN ゲートウェイをアクティブ/アクティブ モードで構成します。 アクティブ/アクティブおよびアクティブ/スタンバイ モードには、いくつかの違いがあります。 その他のプロパティは、非アクティブ/アクティブのゲートウェイと同じです。 

* アクティブ/アクティブ ゲートウェイには、2 つのゲートウェイ IP 構成と 2 つのパブリック IP アドレスがあります。
* アクティブ/アクティブ ゲートウェイでは、アクティブ/アクティブ設定が有効になっています。
* 仮想ネットワーク ゲートウェイの SKU では Basic または Standard は使用できません。

既に VPN ゲートウェイを使用している場合は、[既存の VPN ゲートウェイ](#update)をアクティブ/スタンバイ モードからアクティブ/アクティブ モードに、またはアクティブ/アクティブからアクティブ/スタンバイ モードに更新できます。

## <a name="create-a-vnet"></a><a name="vnet"></a>VNet を作成する

使用したい VNet がまだない場合は、次の値を使用して VNet を作成します。

* **[リソース グループ]:** TestRG1
* **[名前]:** VNet1
* **[リージョン]:** (米国) 米国東部
* **IPv4 アドレス空間:** 10.1.0.0/16
* **サブネット名:** FrontEnd
* **サブネット アドレス空間:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-an-active-active-vpn-gateway"></a><a name="gateway"></a>アクティブ/アクティブ VPN ゲートウェイを作成する

この手順では、VNet のアクティブ/アクティブな仮想ネットワーク ゲートウェイ (VPN ゲートウェイ) を作成します。 選択したゲートウェイ SKU によっては、ゲートウェイの作成に 45 分以上かかる場合も少なくありません。

次の値を使用して仮想ネットワーク ゲートウェイを作成します。

* **[名前]:** VNet1GW
* **[リージョン]:** 米国東部
* **ゲートウェイの種類:** VPN
* **VPN の種類:** ルート ベース
* **SKU:** VpnGw2
* **世代:** Generation2
* **仮想ネットワーク:** VNet1
* **ゲートウェイ サブネットのアドレス範囲:** 10.1.255.0/27
* **[パブリック IP アドレス]** : 新規作成
* **パブリック IP アドレス名:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-portal-include.md)]
[!INCLUDE [Configure PIP settings](../../includes/vpn-gateway-add-gw-pip-active-portal-include.md)]

デプロイの状態は、ゲートウェイの [概要] ページで確認できます。 ゲートウェイの作成後は、ポータルの仮想ネットワークを調べることで、ゲートウェイに割り当てられている IP アドレスを確認できます。 ゲートウェイは、接続されたデバイスとして表示されます。

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="update-an-existing-vpn-gateway"></a><a name ="update"></a>既存の VPN ゲートウェイを更新する

このセクションでは、既存の Azure VPN ゲートウェイをアクティブ/スタンバイからアクティブ/アクティブ モードに、またアクティブ/アクティブからアクティブ/スタンバイ モードに変更します。 アクティブ/スタンバイ ゲートウェイをアクティブ/アクティブに変更する場合は、別のパブリック IP アドレスを作成して、第 2 のゲートウェイ IP 構成を追加します。 

### <a name="change-active-standby-to-active-active"></a>アクティブ/スタンバイからアクティブ/アクティブに変更する

アクティブ/スタンバイ モードのゲートウェイをアクティブ/アクティブ モードに変換するには、次の手順に従います。 [Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)を使用してゲートウェイを作成した場合は、このページで SKU をアップグレードすることもできます。

1. 仮想ネットワーク ゲートウェイのページに移動します。

1. 左側のメニューで **[構成]** を選択します。

1. **[構成]** ページで、次の設定を構成します。 

   * [アクティブ/アクティブ モード] を **[有効]** に変更します。
   * **[別のゲートウェイ IP 構成の作成]** をクリックします。

   :::image type="content" source="./media/active-active-portal/configuration.png" alt-text="[構成] ページを示すスクリーンショット。":::

1. **[パブリック IP アドレスの選択]** ページで、条件を満たす既存のパブリック IP アドレスを指定するか、 **[+新規作成]** を選択して、2 番目の VPN ゲートウェイ インスタンスに使用する新しいパブリック IP アドレスを作成します。

1. **[パブリック IP アドレスの作成]** ページで、 **[Basic]** SKU を選択してから **[OK]** をクリックします。

1. **[構成]** ページの上部にある **[保存]** をクリックします。 この更新が完了するまでに 30 - 45 分程度かかる場合があります。

### <a name="change-active-active-to-active-standby"></a>アクティブ/アクティブからアクティブ/スタンバイに変更する

アクティブ/アクティブ モードのゲートウェイをアクティブ/スタンバイ モードに変換するには、次の手順に従います。

1. 仮想ネットワーク ゲートウェイのページに移動します。

1. 左側のメニューで **[構成]** を選択します。

1. **[構成]** ページで、[アクティブ/アクティブ モード] を **[無効]** に変更します。

1. **[構成]** ページの上部にある **[保存]** をクリックします。

## <a name="next-steps"></a>次のステップ

接続を構成する場合は、次の記事を参照してください。

* [サイト間 VPN 接続](./tutorial-site-to-site-portal.md)
* [VNet 間接続](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md#configure-the-vnet1-gateway-connection)