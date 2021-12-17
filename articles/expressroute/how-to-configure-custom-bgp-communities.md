---
title: Azure ExpressRoute プライベート ピアリングのカスタム BGP コミュニティを構成する (プレビュー)
description: 新規または既存の仮想ネットワークに BGP コミュニティ値を適用または更新する方法について説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/18/2021
ms.author: duau
ms.openlocfilehash: da3be1f9fbfc2872c31ed4f40d01a1d38ef300ef
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477877"
---
# <a name="configure-custom-bgp-communities-for-azure-expressroute-private-peering-preview"></a>Azure ExpressRoute プライベート ピアリングのカスタム BGP コミュニティを構成する (プレビュー)

BGP コミュニティは、コミュニティ値でタグ付けされた IP プレフィックスのグループです。 この値は、ルーターのインフラストラクチャでルーティングの決定を行うために使用できます。 BGP コミュニティ タグを使用すると、Azure からオンプレミスに送信されるトラフィックに対してフィルターを適用することや、ルーティング設定を指定することができます。 この記事では、Azure PowerShell を使用して対象の仮想ネットワークにカスタム BGP コミュニティ値を適用する方法について説明します。 構成が完了すると、リージョンの BGP コミュニティの値と、対象の仮想ネットワークのカスタム コミュニティ値を表示できます。 この値は、その仮想ネットワークから発信された場合に ExpressRoute を使用して送信される送信トラフィックに使用されます。

## <a name="prerequisites"></a>前提条件

* 構成を開始する前に、[前提条件](expressroute-prerequisites.md)、[ルーティングの要件](expressroute-routing.md)、[ワークフロー](expressroute-workflows.md)を確認します。

* アクティブな ExpressRoute 回線が必要です。 
  * 手順に従って、 [ExpressRoute 回線を作成](expressroute-howto-circuit-arm.md) し、接続プロバイダー経由で回線を有効にしてください。 
  * 回線用に Azure プライベート ピアリングが構成されていることを確認してください。 ルーティング手順については、 [ルーティングの構成](expressroute-howto-routing-arm.md) に関する記事を参照してください。 
  * エンドツーエンド接続のために、確実に Azure プライベート ピアリングが構成され、ご使用のネットワークと Microsoft の間の BGP ピアリングを確立します。
  
### <a name="working-with-azure-powershell"></a>Azure PowerShell を使用する

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="apply-a-custom-bgp-community-value-for-a-new-virtual-network"></a>新しい仮想ネットワークにカスタム BGP コミュニティ値を適用する

1. 構成を開始するには、Azure アカウントにサインインし、使用するサブスクリプションを選択します。

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

1. 新しい仮想ネットワークを格納するリソース グループを作成します。

    ```azurepowershell-interactive
    $rg = @{
        Name = 'myERRG'
        Location = 'WestUS'
    }
    New-AzResourceGroup @rg
    ```

1. `-BgpCommunity` フラグを使用して、BGP コミュニティ値を適用する新しい仮想ネットワークを作成します。

    ```azurepowershell-interactive
    $vnet = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
        Location = 'WestUS'
        AddressPrefix = '10.0.0.0/16'
        BgpCommunity = '12076:20001'    
    }
    New-AzVirtualNetwork @vnet
    ```
    
    > [!NOTE]
    > カスタム コミュニティ値の前に `12076:` が必要です。
    >

1. 対象の仮想ネットワークを取得し、そのプロパティを確認します。 **RegionalCommunity** 値と **VirtualNetworkCommunity** 値を含む *BgpCommunities* セクションがあります。 *RegionalCommunity* 値は、仮想ネットワークの Azure リージョンに基づいて事前に定義されています。 *VirtualNetworkCommunity* 値は、カスタム定義と一致する必要があります。

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNewtork @virtualnetwork
    ```

## <a name="applying-or-updating-the-custom-bgp-value-for-an-existing-virtual-network"></a>既存の仮想ネットワークにカスタム BGP 値を適用または更新する

1. BGP コミュニティ値を適用または更新する仮想ネットワークを取得し、変数に格納します。

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    $vnet = Get-AzVirtualNewtork @virtualnetwork
    ```

1. 対象の仮想ネットワークの `VirtualNetworkCommunity` 値を更新します。

    ```azurepowershell-interactive
    $vnet.BgpCommunities.VirtualNetworkCommunity = '12076:20002'
    $vnet | Set-AzVirtualNetwork
    ```

    > [!NOTE]
    > カスタム コミュニティ値の前に `12076:` が必要です。
    >

1. 対象の仮想ネットワークを取得し、その更新されたプロパティを確認します。 **RegionalCommunity** 値は、仮想ネットワークの Azure リージョンに基づいて事前に定義されています。 **VirtualNetworkCommunity** 値は、カスタム定義と一致する必要があります。

    ```azurepowershell-interactive
    $virtualnetwork = @{
        Name = 'myVirtualNetwork'
        ResourceGroupName = 'myERRG'
    } 
    Get-AzVirtualNetwork @virtualnetwork
    ```

> [!IMPORTANT]
>  既存の仮想ネットワークが既に ExpressRoute 回線に接続されている場合は、カスタム BGP コミュニティ値を適用した後、ExpressRoute 接続を削除して再作成する必要があります。 詳細については、[仮想ネットワークを ExpressRoute 回線にリンクする](expressroute-howto-linkvnet-arm.md)方法に関するページを参照してください。
>

## <a name="next-steps"></a>次の手順

- [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)。
- [ネットワーク パフォーマンスのトラブルシューティング](expressroute-troubleshooting-network-performance.md)
