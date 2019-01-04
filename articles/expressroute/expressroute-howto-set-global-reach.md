---
title: 'Global Reach を構成する - ExpressRoute: Azure | Microsoft Docs'
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mialdrid
ms.custom: seodec18
ms.openlocfilehash: 3df107f8854469b50c5e8483515388b5c93fb244
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53383274"
---
# <a name="configure-expressroute-global-reach-preview"></a>ExpressRoute Global Reach を構成する (プレビュー)
この記事は、PowerShell を使用して ExpressRoute Global Reach を構成する際に役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。
 
## <a name="before-you-begin"></a>開始する前に
> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。
> 


構成を開始する前に、次のことを確認してください。

* 最新バージョンの Azure PowerShell がインストールされていること。 詳細については、[Azure PowerShell のインストールおよび構成](/powershell/azure/install-azurerm-ps)をご覧ください。
* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解していること。
* ExpressRoute 回線がプロビジョニングされた状態にあること。
* ExpressRoute 回線上に Azure プライベート ピアリングが構成されていること。  

### <a name="sign-in-to-your-azure-account"></a>Azure アカウントへのサインイン
構成を開始するには、Azure アカウントにサインインします。 

昇格された特権で PowerShell コンソールを開き、アカウントに接続します。 このコマンドでは、Azure アカウントのサインイン資格情報を入力するよう求められます。  

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
任意の 2 つの ExpressRoute 回線間で ExpressRoute Global Reach を有効にすることができるのは、それらがサポートされている国に配置され、かつ異なるピアリングの場所で作成されている場合だけです。 サブスクリプションが両方の回線を所有している場合は、以降のセクションで構成を実行するために、どちらかの回線を選択できます。 

2 つの回線が異なる Azure サブスクリプションに含まれている場合は、1 つの Azure サブスクリプションからの承認が必要です。 その後、もう一方の Azure サブスクリプションで構成コマンドを実行するときに承認キーを渡します。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を有効にする

次のコマンドを使用して回線 1 と回線 2 を取得します。 2 つの回線は同じサブスクリプションにあります。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

回線 1 に対して次のコマンドを実行し、回線 2 のプライベート ピアリング ID を渡します。 このコマンドを実行するときは、次のことに注意してください。

* プライベート ピアリング ID は、次の例のようになります。 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
* *-AddressPrefix* は、/29 IPv4 サブネット ("10.0.0.0/29" など) である必要があります。 このサブネット内の IP アドレスを使用して、2 つの ExpressRoute 回線間の接続を確立します。 このサブネット内のアドレスを Azure 仮想ネットワークやオンプレミス ネットワークでは使用しないでください。

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

回線 1 上の構成を次のように保存します。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプション内に承認が生成され、その承認キーが回線 1 に渡されます。

承認キーを生成します。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
回線 2 のプライベート ピアリング ID と承認キーを書きとめます。

回線 1 に対して次のコマンドを実行します。 回線 2 のプライベート ピアリング ID と承認キーを渡します。
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

この構成は回線 1 に保存します。
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

## <a name="get-and-verify-the-configuration"></a>構成を取得して確認する

構成が作成された回線 (たとえば、前の例では回線 1) 上の構成を確認するには、次のコマンドを使用します。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

単純に PowerShell で *$ckt1* を実行すると、出力に *CircuitConnectionStatus* が表示されます。 これは、接続が確立されているかどうか、つまり "Connected" または "Disconnected" を通知します。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を無効にする

接続を無効にするには、構成が作成された回線 (たとえば、前の例では回線 1) に対してコマンドを実行します。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Get 操作を実行して状態を確認できます。 

前の操作が完了すると、ExpressRoute 回線を経由したオンプレミス ネットワーク間の接続は存在しなくなります。 


## <a name="next-steps"></a>次の手順
1. [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
2. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)


