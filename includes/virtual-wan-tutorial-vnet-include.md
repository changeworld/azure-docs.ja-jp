---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1fae3c3889242dfbf8f270d3762ea7434ceda6da
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "47004170"
---
VNet がまだない場合は、PowerShell を使用して簡単に作成できます。 仮想ネットワークは、Azure portal を使用して作成することもできます。

* 作成する VNet のアドレス空間が接続する他の VNet のアドレス範囲やオンプレミス ネットワークのアドレス空間と重複しないことを確認してください。 
* VNet が既にある場合は、VNet が必要な条件を満たしていて、仮想ネットワーク ゲートウェイがないことを確認します。

この記事の [使ってみる] をクリックして PowerShell コンソールを開くと、VNet を簡単に作成することができます。 値を調整し、コマンドをコピーしてコンソール ウィンドウに貼り付けます。

### <a name="create-a-resource-group"></a>リソース グループの作成

PowerShell コマンドを調整した後、リソース グループを作成します。

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>VNet を作成する

PowerShell コマンドを調整して、ご使用の環境に適した VNet を作成します。

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzureRmVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```