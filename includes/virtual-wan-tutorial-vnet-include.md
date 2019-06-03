---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 04/23/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 40c8cb41ad3bcd46e9973a5f96134ff1bfd02fd2
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/23/2019
ms.locfileid: "66150802"
---
VNet を簡単に作成するために、この記事の [使ってみる] をクリックして PowerShell コンソールを開くことができます。 値を調整し、コマンドをコピーしてコンソール ウィンドウに貼り付けます。 Az モジュールと AzureRM の互換性の詳細については、「[Introducing the new Azure PowerShell Az module (新しい Azure PowerShell Az モジュールの概要)](/powershell/azure/new-azureps-module-az)」を参照してください。 Az モジュールのインストール手順については、[Azure PowerShell のインストール](/powershell/azure/install-az-ps)を参照してください。

作成する VNet のアドレス空間が接続する他の VNet のアドレス範囲やオンプレミス ネットワークのアドレス空間と重複しないことを確認してください。

### <a name="create-a-resource-group"></a>リソース グループの作成

使用するリソース グループがまだ存在しない場合は、新たに作成します。 使用するリソース グループ名を反映するように PowerShell コマンドを調整し、次のコマンドレットを実行します。

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName WANTestRG -Location WestUS
```

### <a name="create-a-vnet"></a>VNet を作成する

PowerShell コマンドを調整して、ご使用の環境に適した VNet を作成します。

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name FrontEnd -AddressPrefix "10.1.0.0/24"
$vnet   = New-AzVirtualNetwork `
            -Name WANVNet1 `
            -ResourceGroupName WANTestRG `
            -Location WestUS `
            -AddressPrefix "10.1.0.0/16" `
            -Subnet $fesub1
```