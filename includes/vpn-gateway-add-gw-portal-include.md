---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/16/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5b47f21ba6597380ca79bfce03feb8edd62aa5ae
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/22/2021
ms.locfileid: "114439732"
---
1. **[リソース、サービス、ドキュメントの検索 (G+/)]** に「**仮想ネットワーク ゲートウェイ**」と入力します。 検索結果で **仮想ネットワーク ゲートウェイ** を見つけて、それを選択します。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/search.png" alt-text="検索フィールドのスクリーンショット。" lightbox="./media/vpn-gateway-add-gw-portal/search-expand.png":::

1. **[仮想ネットワーク ゲートウェイ]** ページで、 **[+ 作成]** を選択します。 **[仮想ネットワーク ゲートウェイの作成]** ページが開きます。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/create.png" alt-text=" [作成] が強調表示されている [仮想ネットワーク ゲートウェイ] ページのスクリーンショット。":::
1. **[基本]** タブで、 **[プロジェクトの詳細]** と **[インスタンスの詳細]** の各値を入力します。

   :::image type="content" source="./media/vpn-gateway-add-gw-portal/instance-details.png" alt-text="インスタンスのフィールドのスクリーンショット。":::

   * **サブスクリプション**:使用するサブスクリプションをドロップダウンから選択します。
   * **リソース グループ**:この設定は、このページで仮想ネットワークを選択すると自動入力されます。
   * **Name**:ゲートウェイに名前を付けます。 ゲートウェイの名前付けは、ゲートウェイ サブネットの名前付けと同じではありません。 作成するゲートウェイ オブジェクトの名前です。
   * **[リージョン]** :このリソースを作成するリージョンを選択します。 ゲートウェイのリージョンは、仮想ネットワークと同じである必要があります。
   * **[ゲートウェイの種類]** : **[VPN]** を選択します。 VPN Gateway では、仮想ネットワーク ゲートウェイの種類として **VPN** を使用します。
   * **VPN の種類**:構成に指定されている VPN の種類を選択します。 ほとんどの構成で [VPN の種類] は [ルート ベース] にする必要があります。
   * **SKU**: 使用するゲートウェイ SKU をドロップダウンから選択します。 ドロップダウン リストに表示される SKU は、選択した VPN の種類によって異なります。 使用する機能をサポートする SKU を選択してください。 ゲートウェイの SKU の詳細については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku)」を参照してください。
   * **世代**: 使用する世代を選択します。 詳細については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。
   * **仮想ネットワーク**:ドロップダウンから、このゲートウェイの追加先の仮想ネットワークを選択します。
   * **[ゲートウェイ サブネットのアドレス範囲]** : このフィールドは、VNet にゲートウェイ サブネットがない場合にのみ表示されます。 これは /27 またはそれ以上 (/26、/25 など) を指定することをお勧めします。 これにより、ExpressRoute ゲートウェイの追加など、将来の変更のために十分な IP アドレスが許可されます。 /28 より小さい範囲を作成することはお勧めしません。 既にゲートウェイ サブネットがある場合は、仮想ネットワークから GatewaySubnet の詳細を表示できます。 範囲を表示するには、 **[サブネット]** をクリックします。 範囲を変更する場合は、GatewaySubnet を削除して再作成できます。