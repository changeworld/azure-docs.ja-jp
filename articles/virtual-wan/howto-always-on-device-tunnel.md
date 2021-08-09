---
title: Always-On VPN トンネルの構成
titleSuffix: Azure Virtual WAN
description: Virtual WAN に Always On VPN デバイス トンネルを構成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 05/26/2021
ms.author: cherylmc
ms.openlocfilehash: 69899179b65400e8a3b81f497e950aa3123c624e
ms.sourcegitcommit: e1d5abd7b8ded7ff649a7e9a2c1a7b70fdc72440
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/27/2021
ms.locfileid: "110579756"
---
# <a name="configure-an-always-on-vpn-device-tunnel-for-virtual-wan"></a>Virtual WAN に Always On VPN デバイス トンネルを構成する

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="prerequisites"></a>前提条件

ポイント対サイト構成を作成し、仮想ハブの割り当てを編集する必要があります。 手順については、次のセクションを参照してください。

* [P2S の構成を作成する](virtual-wan-point-to-site-portal.md#p2sconfig)
* [P2S ゲートウェイを使用してハブを作成する](virtual-wan-point-to-site-portal.md#hub)

## <a name="configure-the-device-tunnel"></a>デバイス トンネルの構成

[!INCLUDE [device tunnel](../../includes/vpn-gateway-vwan-always-on-device.md)]

## <a name="to-remove-a-profile"></a>プロファイルを削除するには

プロファイルを削除するには、次のコマンドを実行します。

![Remove-VpnConnection -Name MachineCertTest コマンドを実行している PowerShell ウィンドウを示すスクリーンショット。](./media/howto-always-on-device-tunnel/cleanup.png)

## <a name="next-steps"></a>次のステップ

Virtual WAN の詳細については、[FAQ](virtual-wan-faq.md) を参照してください。