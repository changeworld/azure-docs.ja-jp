---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 76a602ae722bd975e634631819ebc703e8896c98
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/25/2020
ms.locfileid: "96028544"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続していない場合)

アドレス プレフィックスを追加するには:

1. LocalNetworkGateway の変数を設定します。

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. プレフィックスを変更します。

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24','10.101.2.0/24')
   ```

アドレス プレフィックスを削除するには:

  不要になったプレフィックスは削除します。 この例では、プレフィックス 10.101.2.0/24 (前の例に含まれる) が不要になったため、ローカル ネットワーク ゲートウェイを更新してそのプレフィックスを削除します。

1. LocalNetworkGateway の変数を設定します。

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
2. 更新されたプレフィックスでゲートウェイを設定します。

   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>ローカル ネットワーク ゲートウェイ IP アドレスのプレフィックスを変更するには (ゲートウェイに接続している場合)

ゲートウェイ接続がある場合に、ローカル ネットワーク ゲートウェイに含まれている IP アドレスのプレフィックスを追加または削除するには、次の手順を順番に実行する必要があります。 これに伴い、VPN 接続のためにある程度のダウンタイムが発生します。 IP アドレスのプレフィックスを変更するときに、VPN ゲートウェイを削除する必要はありません。 削除が必要になるのは、接続のみです。

1. 接続を削除します。

   ```azurepowershell-interactive
   Remove-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. 変更されたアドレス プレフィックスでローカル ネットワーク ゲートウェイを設定します。
   
   LocalNetworkGateway の変数を設定します。

   ```azurepowershell-interactive
   $local = Get-AzLocalNetworkGateway -Name Site1 -ResourceGroupName TestRG1
   ```
   
   プレフィックスを変更します。
   
   ```azurepowershell-interactive
   Set-AzLocalNetworkGateway -LocalNetworkGateway $local `
   -AddressPrefix @('10.101.0.0/24','10.101.1.0/24')
   ```
3. 接続を作成します。 この例では、IPsec という接続の種類を構成します。 接続を作成し直すときは、実際の構成で指定した接続の種類を使用してください。 その他の種類の接続については、 [PowerShell コマンドレット](/powershell/module/Azurerm.Network/New-AzureRmVirtualNetworkGatewayConnection) のページを参照してください。
   
   VirtualNetworkGateway の変数を設定します。

   ```azurepowershell-interactive
   $gateway1 = Get-AzVirtualNetworkGateway -Name VNet1GW  -ResourceGroupName TestRG1
   ```
   
   接続を作成します。 この例では、手順 2. で設定した $local という変数を使用しています。

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 `
   -ResourceGroupName TestRG1 -Location 'East US' `
   -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
   -ConnectionType IPsec `
   -RoutingWeight 10 -SharedKey 'abc123'
   ```