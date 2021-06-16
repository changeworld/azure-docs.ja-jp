---
title: Azure PowerShell サンプル スクリプト - デバイス構成テンプレートのダウンロード | Microsoft Docs
description: PowerShell スクリプトを使用して、接続用の VPN デバイス構成テンプレートをダウンロードする方法について説明します。
services: vpn-gateway
documentationcenter: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.devlang: powershell
ms.topic: sample
ms.date: 01/09/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5a9188ea90b2b807e353c3214caf1a0bfc57706b
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/28/2021
ms.locfileid: "110681466"
---
# <a name="download-vpn-device-template-using-powershell"></a>PowerShell を使用して VPN デバイス テンプレートをダウンロードする

このスクリプトは、接続に使用する VPN デバイス テンプレートをダウンロードします

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```azurepowershell-interactive
# Declare variables
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration template for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG `
-DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成したリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) コマンドを使用してリソース グループを削除します。 これでリソース グループとそれに含まれるすべてのリソースが削除されます。

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、以下のコマンドを実行してデプロイを作成します。 表内の各項目は、コマンドごとのドキュメントにリンクされています。

| コマンド | メモ |
|---|---|
| [Get-AzVirtualNetworkGatewaySupportedVpnDevice](/powershell/module/az.network/Get-azVirtualNetworkGatewaySupportedVpnDevice) | 利用可能なすべての VPN デバイスのモデルとバージョンを一覧表示します。 |
| [Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript](/powershell/module/az.network/Get-azVirtualNetworkGatewayConnectionVpnDeviceConfigScript) | 接続の構成テンプレートをダウンロードします。 |

## <a name="next-steps"></a>次のステップ

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。
