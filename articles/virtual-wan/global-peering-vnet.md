---
title: Azure Virtual WAN のグローバル VNet ピアリングを構成する | Microsoft Docs
description: Virtual WAN ハブに別のリージョンにある VNet を接続します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/27/2020
ms.locfileid: "73587068"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Virtual WAN のグローバル VNet ピアリング (クロスリージョン VNet) を構成する

Virtual WAN ハブに別のリージョンにある VNet を接続できます。

## <a name="before-you-begin"></a>開始する前に

以下の条件を満たしていることを確認します。

* クロスリージョン VNet (スポーク) が別の Virtual WAN ハブに接続されていない。 スポークは、1 つの仮想ハブにのみ接続できます。
* VNet (スポーク) に仮想ネットワーク ゲートウェイ (Azure VPN Gateway や ExpressRoute 仮想ネットワーク ゲートウェイなど) が含まれていない。 VNet に仮想ネットワーク ゲートウェイが含まれている場合は、スポーク VNet をハブに接続する前に、ゲートウェイを削除する必要があります。

## <a name="register-this-feature"></a><a name="register"></a>この機能を登録する

この機能は、PowerShell を使用して登録できます。 下の例で [試してみる] を選択すると、Azure Cloud Shell が開いて、PowerShell コマンドレットをコンピューターにローカルにインストールする必要がなくなります。 必要に応じて、'Select-AzSubscription -SubscriptionId <subid>' コマンドレットを使用してサブスクリプションを変更できます。

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>登録を確認する

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>VNet をハブに接続する

この手順では、ハブとクロスリージョン VNet の間にピアリング接続を作成します。 接続する VNet ごとにこれらの手順を繰り返します。

1. 仮想 WAN のページで、 **[仮想ネットワーク接続]** をクリックします。
2. 仮想ネットワーク接続のページで、 **[+ 接続の追加]** をクリックします。
3. **[接続の追加]** ページで、次のフィールドに入力します。

    * **[接続名]** - 接続に名前を付けます。
    * **[ハブ]** - この接続に関連付けるハブを選択します。
    * **[サブスクリプション]** - サブスクリプションを確認します。
    * **[仮想ネットワーク]** - このハブに接続する仮想ネットワークを選択します。 仮想ネットワークに既存の仮想ネットワーク ゲートウェイを設定することはできません。
4. **[OK]** をクリックして、ピアリング接続を作成します。

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。