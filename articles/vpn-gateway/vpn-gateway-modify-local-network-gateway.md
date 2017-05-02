---
title: "ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスと VPN ゲートウェイの IP アドレスの変更| Azure| PowerShell| Microsoft Docs"
description: "この記事では、ローカル ネットワーク ゲートウェイの IP アドレスのプレフィックスを変更する手順について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c7db48f-d09a-44e7-836f-1fb6930389df
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: a26cbe2172dc27c152246d70b7f6b504ec4a08a5
ms.lasthandoff: 04/12/2017


---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>PowerShell を使用したローカル ネットワーク ゲートウェイの設定の変更
ローカル ネットワーク ゲートウェイの AddressPrefix または GatewayIPAddress の設定が変わることがあります。 次の手順に従うと、ローカル ネットワーク ゲートウェイの設定を変更することができます。 これらの設定は、Azure ポータルで変更することもできます。

## <a name="before-you-begin"></a>開始する前に
Azure リソース マネージャー PowerShell コマンドレットの最新版をインストールする必要があります。 PowerShell コマンドレットのインストールの詳細については、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs) 」を参照してください。

## <a name="to-modify-ip-address-prefixes"></a>IP アドレスのプレフィックスを変更するには
[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="to-modify-the-gateway-ip-address"></a>ゲートウェイ IP アドレスを変更するには
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>次のステップ
ゲートウェイの接続を確認することができます。 「 [Verify a gateway connection (ゲートウェイの接続を確認する)](vpn-gateway-verify-connection-resource-manager.md)」を参照してください。


