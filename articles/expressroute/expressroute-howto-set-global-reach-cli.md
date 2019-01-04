---
title: 'ExpressRoute Global Reach を構成する: Azure CLI | Microsoft Docs'
description: この記事では、ExpressRoute 回線を相互にリンクして、オンプレミス ネットワーク間にプライベート ネットワークを構築し、Global Reach を有効にする方法について説明します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 9a8e0a5df9383d8e3d7159aa916b0e4fbfeea948
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384058"
---
# <a name="configure-expressroute-global-reach-using-azure-cli-preview"></a>Azure CLI を使用して ExpressRoute Global Reach を構成する (プレビュー)
この記事は、Azure CLI を使用して ExpressRoute Global Reach を構成するときに役立ちます。 詳細については、[ExpressRouteRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。
 
## <a name="before-you-begin"></a>開始する前に
> [!IMPORTANT]
> このパブリック プレビュー版はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することは避けてください。 特定の機能はサポート対象ではなく、機能が制限されることがあるか、Azure の場所によっては利用できない場合があります。 詳しくは、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」をご覧ください。
> 


構成を開始する前に、次の要件を確認する必要があります。

* 最新バージョンの Azure CLI をインストールします。 [Azure CLI のインストール](/cli/azure/install-azure-cli)と [Azure CLI の使用開始](/cli/azure/get-started-with-azure-cli)に関する各ページを参照してください。
* ExpressRoute 回線のプロビジョニング [ワークフロー](expressroute-workflows.md)を理解します。
* ExpressRoute 回線がプロビジョニング済み状態であることを確認します。
* Azure プライベート ピアリングが ExpressRoute 回線に構成されていることを確認します。  

### <a name="log-into-your-azure-account"></a>Azure アカウントにログインする
構成を開始するには、Azure アカウントにログインする必要があります。 既定のブラウザーが開き、Azure アカウントのログイン資格情報の入力を求められます。  

```azurecli
az login
```

複数の Azure サブスクリプションを所有している場合には、アカウントのサブスクリプションをすべて確認します。

```azurecli
az account list
```

使用するサブスクリプションを指定します。

```azurecli
az account set --subscription <your subscription ID>
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>構成のために ExpressRoute 回線を特定する
2 つの ExpressRoute 回線がサポートされている国にあり、異なるピアリング拠点で作成されている場合に限り、その回線間で ExpressRoute Global Reach を有効にすることができます。 サブスクリプションが両方の回線を所有している場合は、以下のセクションで説明するように、いずれかの回線を選択して構成を実行することができます。 2 つの回線が異なる Azure サブスクリプションにある場合、一方の Azure サブスクリプションで構成コマンドを実行するときに、もう一方の Azure サブスクリプションからの承認が必要であり、承認キーを渡すことになります。

## <a name="enable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を有効にする

接続を有効にするコマンドを実行するときは、次の値を考慮してください。

* *peer-circuit* は、完全なリソース ID にする必要があります。 例:  

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}
  ```
* *-AddressPrefix* は /29 IPv4 サブネットにする必要があります (例:"10.0.0.0/29")。 このサブネット内の IP アドレスを使用して 2 つの ExpressRoute 回線間に接続を確立します。 Azure VNet またはオンプレミス ネットワークでは、このサブネット内のアドレスを使用しないでください。

次の CLI を実行して、2 つの ExpressRoute 回線を接続します。 次の例のコマンドを使用します。

```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29>
```

CLI の出力は次の例のようになります。

```azurecli
{
  "addressPrefix": "<__.__.__.__/29>",
  "authorizationKey": null,
  "circuitConnectionStatus": "Connected",
  "etag": "W/\"48d682f9-c232-4151-a09f-fab7cb56369a\"",
  "expressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<ResourceGroupName>"
  },
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit1Name>/peerings/AzurePrivatePeering/connections/<ConnectionName>",
  "name": "<ConnectionName>",
  "peerExpressRouteCircuitPeering": {
    "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/peerings/AzurePrivatePeering",
    "resourceGroup": "<Circuit2ResourceGroupName>"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "<ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/peerings/connections"
}
```

上記の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>異なる Azure サブスクリプションの ExpressRoute 回線

2 つの回線が同じ Azure サブスクリプション内にない場合は、承認が必要です。 次の構成では、回線 2 のサブスクリプションで承認が生成され、承認キーが回線 1 に渡されます。

承認キーを生成します。 
```azurecli
az network express-route auth create --circuit-name <Circuit2Name> -g <Circuit2ResourceGroupName> -n <AuthorizationName>
```

CLI の出力は次のようになります。

```azurecli
{
  "authorizationKey": "<authorizationKey>",
  "authorizationUseStatus": "Available",
  "etag": "W/\"cfd15a2f-43a1-4361-9403-6a0be00746ed\"",
  "id": "/subscriptions/<SubscriptionID>/resourceGroups/<Circuit2ResourceGroupName>/providers/Microsoft.Network/expressRouteCircuits/<Circuit2Name>/authorizations/<AuthorizationName>",
  "name": "<AuthorizationName>",
  "provisioningState": "Succeeded",
  "resourceGroup": "<Circuit2ResourceGroupName>",
  "type": "Microsoft.Network/expressRouteCircuits/authorizations"
}
```

回線 2 のリソース ID と認可キーをメモします。

回線 1 に対して次のコマンドを実行します。 回線 2 のリソース ID と認可キーを渡します 
```azurecli
az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
```

上記の操作が完了したら、2 つの ExpressRoute 回線を介して、オンプレミス ネットワーク間の接続を両側で行う必要があります。

## <a name="get-and-verify-the-configuration"></a>構成を取得して確認する

構成が行われた回線 (つまり、上記の例の回線 1) で構成を確認するには、次のコマンドを使用します。

```azurecli
az network express-route show -n <CircuitName> -g <ResourceGroupName>
```

CLI の出力では、*CircuitConnectionStatus* が表示されます。 2 つの回線の間の接続が確立されている場合は "Connected"、確立されていない場合は "Disconnected" と表示されます。 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>オンプレミス ネットワーク間の接続を無効にする

無効にするには、構成が行われた回線 (つまり、上記の例の回線 1) に対してコマンドを実行します。

```azurecli
az network express-route peering connection delete -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName>
```

Show CLI を実行して状態を確認できます。 

上記の操作が完了すると、ExpressRoute 回線を介したオンプレミス ネットワーク間の接続は解除されます。 


## <a name="next-steps"></a>次の手順
* [ExpressRoute Global Reach について詳しく学習する](expressroute-global-reach.md)
* [ExpressRoute 接続を確認する](expressroute-troubleshooting-expressroute-overview.md)
* [ExpressRoute 回線を Azure 仮想ネットワークにリンクする](expressroute-howto-linkvnet-arm.md)


