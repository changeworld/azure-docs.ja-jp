---
title: 仮想ハブ (プレビュー) を使用して BGP ピアリングを作成する - Azure portal
titleSuffix: Azure Virtual WAN
description: Virtual WAN ハブ ルーターで BGP ピアリングを作成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 08/06/2021
ms.author: cherylmc
ms.openlocfilehash: 6c1d845e4f4ad3a61e3131f6451e12070f2af48c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781130"
---
# <a name="how-to-create-bgp-peering-with-virtual-hub-preview---azure-portal"></a>仮想ハブ (プレビュー) を使用して BGP ピアリングを作成する方法 - Azure portal

この記事は、Azure portal を使用して、仮想ネットワーク内のネットワーク仮想アプライアンス (NVA) とピアリングするように Azure Virtual WAN ハブ ルーターを構成するのに役立ちます。 仮想ハブ ルーターは、Virtual WAN ハブに接続されているスポーク VNet 内の NVA からルートを学習します。 仮想ハブ ルーターは、NVA への仮想ネットワーク ルートもアドバタイズします。 詳細については、「[シナリオ: 仮想ハブを使用した BGP ピアリング](scenario-bgp-peering-hub.md)」を参照してください。

[!INCLUDE [Gated public preview SLA link](../../includes/virtual-wan-gated-public-preview-sla.md)]

:::image type="content" source="./media/create-bgp-peering-hub-portal/diagram.png" alt-text="構成の図。":::

## <a name="prerequisites"></a>前提条件

> [!IMPORTANT]
> Virtual WAN ハブを使用した BGP ピアリング機能は、現在、限定パブリック プレビュー段階にあります。 この機能を試したい場合は、 **previewbgpwithvhub@microsoft.com** まで、Virtual WAN リソースのリソース ID と共に電子メールを送信してください。 
>
> リソース ID を見つけるには、Azure portal を開いて Virtual WAN リソースにナビゲートし、 **[設定] > [プロパティ] > [リソース ID]** をクリックします。<br> 例: `/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualWans/<virtualWANname>`
>

構成を開始する前に、以下の条件を満たしていることを確認します。

[!INCLUDE [Before you begin](../../includes/virtual-wan-before-include.md)]

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>仮想 WAN を作成する

[!INCLUDE [Create a virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>ハブを作成する

ハブは、サイト間、ExpressRoute、またはポイント対サイト機能のためのゲートウェイを含めることができる仮想ネットワークです。 ハブが作成されると、サイトをアタッチしていない場合でも、ハブに対して課金されます。

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-s2s-hub-include.md)]

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>VNet をハブに接続する

このセクションでは、ハブと VNet の間に接続を作成します。

[!INCLUDE [Connect a VNet to a hub](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="configure-a-bgp-peer"></a>BGP ピアを構成する

1.  [https://aka.ms/azurecortexv2](https://aka.ms/azurecortexv2) を使用し、[Azure プレビュー ポータル](https://aka.ms/azurecortexv2)を開きます。 Virtual WAN ハブを使用した BGP ピアリング機能は現在管理プレビュー段階にあり、構成ページは通常の Azure portal では使用できません。

1.  仮想 WAN のポータル ページの **[接続]** セクションで、 **[ハブ]** を選択してハブのリストを表示します。 BGP ピアを構成するハブをクリックします。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/hubs.png" alt-text="ハブのスクリーンショット。":::

1.  **[仮想ハブ]** ページの **[ルーティング]** セクションで、 **[BGP ピア]** を選択し、 **[+ 追加]** をクリックして、BGP ピアを追加します。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/bgp-peers.png" alt-text="3.":::

1.  **[Add BGP Peer]\(BGP ピアを追加\)** ページですべてのフィールドに入力します。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/add-peer.png" alt-text="4.":::

    * **[名前]** – 特定の BGP ピアを識別するためのリソース名。 
    * **[ASN]** – BGP ピアの ASN。
    * **[IPv4 アドレス]** – BGP ピアの IPv4 アドレス。
    * **[仮想ネットワーク接続]** – BGP ピアをホストする仮想ネットワークに対応する接続識別子を選択します。

1.  **[追加]** をクリックして、BGP ピアの構成を終え、ピアを表示します。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/view-peer.png" alt-text="追加されたピアのスクリーンショット。":::

## <a name="modify-a-bgp-peer"></a>BGP ピアを変更する

1. **仮想ハブ** リソースで、 **[BGP ピア]** をクリックし、BGP ピアを選択します。 **[…]** をクリックし、 次に、 **[編集]** をクリックします。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/modify.png" alt-text="編集のスクリーンショット。":::

1. BGP ピアを変更したら、 **[追加]** をクリックして保存します。

## <a name="delete-a-bgp-peer"></a>BGP ピアを削除する

1. **仮想ハブ** リソースで、 **[BGP ピア]** をクリックし、BGP ピアを選択します。 **[…]** をクリックし、 次に、 **[削除]** をクリックします。

    :::image type="content" source="./media/create-bgp-peering-hub-portal/delete.png" alt-text="ピアの削除のスクリーンショット。":::

## <a name="next-steps"></a>次のステップ

* BGP シナリオの詳細については、「[シナリオ: 仮想ハブを使用した BGP ピアリング](scenario-bgp-peering-hub.md)」を参照してください。
