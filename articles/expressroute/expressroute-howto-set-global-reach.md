---
title: Azure ExpressRoute Global Reach の構成 | Microsoft Docs
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: cherylmc
ms.openlocfilehash: 4006626f9768289e75ccd61a1ef0bad5389f0a7a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071043"
---
# <a name="configure-expressroute-global-reach-preview"></a>ExpressRoute Global Reach の構成 (プレビュー)
この記事は、PowerShell を使用して ExpressRoute Global Reach を構成する際に役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。
 
## <a name="before-you-begin"></a>開始する前に
> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
> 


構成を開始する前に、次の要件を確認する必要があります。

* 最新バージョンの Azure PowerShell をインストールします。 「[Azure PowerShell のインストールと構成の方法](/powershell/azure/install-azurerm-ps)」を参照してください。
* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解します。
* ExpressRoute 回線がプロビジョニング済み状態であることを確認します。
* Azure プライベート ピアリングが ExpressRoute 回線に構成されていることを確認します。  

### <a name="log-into-your-azure-account"></a>Azure アカウントにログインする
構成を開始するには、Azure アカウントにログインする必要があります。 

昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 Azure アカウントのログイン資格情報の入力が求められます。  

```powershell
Connect-AzureRmAccount
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```powershell
Get-AzureRmSubscription
```

使用するサブスクリプションを指定します。

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>構成のために ExpressRoute 回線を特定する
2 つの ExpressRoute 回線がサポートされている国にあり、異なるピアリング拠点で作成されている場合に限り、その回線間で ExpressRoute Global Reach を有効にすることができます。 サブスクリプションが両方の回線を所有している場合は、以下のセクションで説明するように、いずれかの回線を選択して構成を実行することができます。 2 つの回線が異なる Azure サブスクリプションにある場合、一方の Azure サブスクリプションで構成コマンドを実行するときに、もう一方の Azure サブスクリプションからの承認が必要であり、承認キーを渡すことになります。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を有効にする

次のコマンドを使用して回線 1 と回線 2 を取得します。 2 つの回線は同じサブスクリプションにあります。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

回線 1 に対して次のコマンドを実行し、回線 2 のプライベート ピアリングの ID を渡します。

> [!NOTE]
> プライベート ピアリングの ID は、*/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering* のようになります
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* は /29 IPv4 サブネットにする必要があります (例:"10.0.0.0/29")。 このサブネット内の IP アドレスを使用して 2 つの ExpressRoute 回線間に接続を確立します。 Azure VNet またはオンプレミス ネットワークでは、このサブネット内のアドレスを使用しないでください。
> 



この構成は回線 1 に保存します。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

上記の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプションで承認が生成され、承認キーが回線 1 に渡されます。

承認キーを生成します。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
回線 2 のプライベート ピアリング ID と承認キーをメモします。

回線 1 に対して次のコマンドを実行します。 回線 2 のプライベート ピアリング ID と承認キーを渡します 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

この構成は回線 1 に保存します。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

上記の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

## <a name="get-and-verify-the-configuration"></a>構成を取得して確認する

構成が行われた回線 (つまり、上記の例の回線 1) で構成を確認するには、次のコマンドを使用します。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell で単に *$ckt1* を実行すると、出力に *CircuitConnectionStatus* が表示されます。 接続が確立されている場合は "Connected"、確立していない場合は "Disconnected" と表示されます。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を無効にする

無効にするには、構成が行われた回線 (つまり、上記の例の回線 1) に対してコマンドを実行します。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Get 操作を実行して状態を確認できます。 

上記の操作が完了すると、ExpressRoute 回線を介したオンプレミス ネットワーク間の接続は解除されます。 


## <a name="next-steps"></a>次の手順
1. [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
2. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)


