---
title: ポイント対サイト VPN ゲートウェイ クライアントのカスタム ルートをアドバタイズする
titleSuffix: Azure VPN Gateway
description: VPN ゲートウェイ ポイント対サイト クライアントにカスタム ルートをアドバタイズする方法について説明します。 この記事では、VPN クライアント強制トンネリングの手順について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/08/2021
ms.author: cherylmc
ms.openlocfilehash: b37b63fda6b142fc1aba458c007b6fe0eb5db814
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/09/2021
ms.locfileid: "111810950"
---
# <a name="advertise-custom-routes-for-p2s-vpn-clients"></a>P2S VPN クライアント用のカスタム ルートをアドバタイズする

すべてのポイント対サイト VPN クライアントへのカスタム ルートをアドバタイズできます。 たとえば、VNet でストレージ エンドポイントを有効にしているときに、リモート ユーザーが VPN 接続経由でこれらのストレージ アカウントにアクセスできるようにすることができます。 ストレージ エンドポイントの IP アドレスをすべてのリモート ユーザーにアドバタイズして、ストレージ アカウントへのトラフィックをパブリック インターネットではなく VPN トンネル経由にすることができます。 VPN クライアントの強制トンネリングを構成するために、カスタム ルートを使用することもできます。

:::image type="content" source="./media/vpn-gateway-p2s-advertise-custom-routes/custom-routes.png" alt-text="カスタム ルート公開の図。":::

## <a name="advertise-custom-routes"></a>カスタム ルートを公開する

カスタム ルートをアドバタイズするには、`Set-AzVirtualNetworkGateway cmdlet` を使用します。 次の例では、[Contoso ストレージ アカウント テーブル](https://contoso.table.core.windows.net)の IP をアドバタイズする方法を示します。

1. *contoso.table.core.windows.net* を ping し、IP アドレスをメモします。 次に例を示します。

    ```cmd
    C:\>ping contoso.table.core.windows.net
    Pinging table.by4prdstr05a.store.core.windows.net [13.88.144.250] with 32 bytes of data:
    ```

2. 次の PowerShell コマンドを実行します。

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 13.88.144.250/32
    ```

3. 複数のカスタム ルートを追加するには、コンマとスペースを使用してアドレスを区切ります。 次に例を示します。

    ```azurepowershell-interactive
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute x.x.x.x/xx , y.y.y.y/yy
    ```

## <a name="advertise-custom-routes---forced-tunneling"></a>カスタム ルートを公開する - 強制トンネリング

すべてのトラフィックを VPN トンネルに誘導するには、クライアントへのカスタム ルートとして 0.0.0.0/1 と 128.0.0.0/1 を公開します。 0\.0.0.0/0 を 2 つの小さなサブネットに分割する理由は、これらの小さなプレフィックスは、ローカル ネットワーク アダプター上で既に構成されている可能性がある既定のルートより具体的で、トラフィックをルーティングするときに優先されるためです。

1. 強制トンネリングを有効にするには、次のコマンドを使用します。

    ```azurepowershell-interactive    
    $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute 0.0.0.0/1 , 128.0.0.0/1
    ```
## <a name="view-custom-routes"></a>カスタム ルートを表示する

カスタム ルートを表示するには、次の例を使用します。

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  $gw.CustomRoutes | Format-List
  ```
## <a name="delete-custom-routes"></a>カスタム ルートを削除する

カスタム ルートを削除するには、次の例を使用します。

  ```azurepowershell-interactive
  $gw = Get-AzVirtualNetworkGateway -Name <name of gateway> -ResourceGroupName <name of resource group>
  Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -CustomRoute @0
  ```
## <a name="next-steps"></a>次のステップ

詳細な P2S ルーティング情報については、[ポイント対サイト ルーティング](vpn-gateway-about-point-to-site-routing.md)に関するページをご覧ください。
