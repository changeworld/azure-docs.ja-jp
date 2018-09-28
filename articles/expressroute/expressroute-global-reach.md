---
title: Azure ExpressRoute Global Reach | Microsoft Docs
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
documentationcenter: na
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 90f34da10e0193e31a0f70187463799013f52be8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966817"
---
# <a name="link-expressroute-circuits-to-enable-expressroute-global-reach-preview"></a>ExpressRoute 回線をリンクして ExpressRoute Global Reach を有効にする (プレビュー)

ExpressRoute は、複数のオンプレミス ネットワークを Microsoft Cloud に接続することができるプライベートで回復性がある方法です。 プライベート データ センターまたは企業ネットワークから、Azure、Office 365、Dynamics 365 などの多くの Microsoft クラウド サービスにアクセスできます。 たとえば、シリコン バレーに ExpressRoute 回線があるサンフランシスコのブランチ オフィスと、ワシントン DC に ExpressRoute 回線があるボルチモアのブランチ オフィスがあるとします。 

両方のブランチ オフィスは、米国東部および西部両方の Azure リソースと高速接続できますが、ブランス オフィス間で直接データを交換することはできません。 つまり、10.0.1.0/24 は 10.0.3.0/24 および 10.0.4.0/24 とデータを交換できますが、10.0.2.0/24 とは交換できません。

![ない場合][1]

**ExpressRoute Global Reach** を使用すると、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築することができます。 上記の例では、ExpressRoute Global Reach を追加することで、サンフランシスコ オフィス (10.0.1.0/24) が、既存の ExpressRoute 回線や Microsoft のグローバル ネットワークを介してボルチモア オフィス (10.0.2.0/24) とデータを直接交換できます。 

![ある場合][2]

現在、ExpressRoute Global Reach は以下の国でサポートされています。

* 米国
* イギリス 
* 日本

ExpressRoute 回線は、上記の国の [ExpressRoute ピアリング拠点](expressroute-locations.md)に作成する必要があります。 [異なる地政学的地域](expressroute-locations.md)間で ExpressRoute Global Reach を有効にするには、Premium SKU の回線を使用する必要があります。


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
2 つの ExpressRoute 回線がサポートされている国にあり、異なるピアリング拠点で作成されている場合に限り、その回線間で ExpressRoute Global Reach を有効にすることができます。 サブスクリプションが両方の回線を所有している場合は、以下のセクションで説明するように、いずれか回線を選択して構成を実行することができます。 2 つの回線が異なる Azure サブスクリプションにある場合、一方の Azure サブスクリプションで構成コマンドを実行するときに、もう一方の Azure サブスクリプションからの承認が必要であり、承認キーを渡すことになります。

## <a name="to-enable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を有効にするには

次のコマンドを使用して回線 1 と回線 2 を取得します。 2 つの回線は同じサブスクリプションにあります。

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

回線 1 に対して次のコマンドを実行し、回線 2 のプライベート ピアリングの ID を渡します。

プライベート ピアリングの ID は次のようになります。

`/subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering`


```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

*-AddressPrefix* は /29 IPv4 サブネットにする必要があります (例: "10.0.0.0/29")。 このサブネット内の IP アドレスを使用して 2 つの ExpressRoute 回線間に接続を確立します。 Azure VNet またはオンプレミス ネットワークでは、このサブネット内のアドレスを使用しないでください。 


この構成は回線 1 に保存します。

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプションで承認が生成され、承認キーが回線 1 に渡されます。

承認キーを生成します。 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
回線 2 のプライベート ピアリング ID と承認キーをメモします。

回線 1 に対して次のコマンドを実行します。 回線 2 のプライベート ピアリングの ID と承認キーを渡します

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

この構成は回線 1 に保存します。

```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

前の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

## <a name="to-get-and-verify-the-configuration"></a>構成を取得して確認するには

構成が行われた回線で構成を確認するには、次のコマンドを使用します。 この例では、前の例の回線 1 を使用しています。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

PowerShell で単に *$ckt1* を実行すると、出力に *CircuitConnectionStatus* が表示されます。 接続が確立されている場合は "Connected"、確立していない場合は "Disconnected" と表示されます。 

## <a name="to-disable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を無効にするには

無効にするには、構成が行われた回線に対してコマンドを実行します。 この例では、前の例の回線 1 を使用しています。

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Get 操作を実行して状態を確認できます。 

上記の操作が完了すると、ExpressRoute 回線を介したオンプレミス ネットワーク間の接続は解除されます。 

## <a name="next-steps"></a>次の手順
1. [ExpressRoute Global Reach について詳しく学習する](expressroute-faqs.md#globalreach)
2. [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
3. [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)


<!--Image References-->
[1]: ./media/expressroute-global-reach/1.png "Global Reach なしの図"
[2]: ./media/expressroute-global-reach/2.png "Global Reach ありの図"