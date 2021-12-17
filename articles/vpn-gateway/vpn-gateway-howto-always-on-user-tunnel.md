---
title: Always-On VPN ユーザー トンネルの構成
titleSuffix: Azure VPN Gateway
description: VPN ゲートウェイに対して Always On VPN ユーザー トンネルを構成する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 04/29/2021
ms.author: cherylmc
ms.openlocfilehash: 59c12333c6cf8ea9d251ce6387e3dce4ff23468b
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/30/2021
ms.locfileid: "108287322"
---
# <a name="configure-an-always-on-vpn-user-tunnel"></a>Always On VPN ユーザー トンネルの構成

[!INCLUDE [intro](../../includes/vpn-gateway-vwan-always-on-intro.md)]

## <a name="configure-the-gateway"></a>ゲートウェイを構成する

 IKEv2 と証明書ベースの認証を使用するように VPN ゲートウェイを構成するには、[ポイント対サイト VPN 接続の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事の手順に従ってください。

## <a name="configure-a-user-tunnel"></a>ユーザー トンネルを構成する

[!INCLUDE [user configuration](../../includes/vpn-gateway-vwan-always-on-user.md)]

## <a name="to-remove-a-profile"></a>プロファイルを削除するには

プロファイルを削除するには、次の手順に従います。

1. 次のコマンドを実行します。

   ```powershell
   C:\> Remove-VpnConnection UserTest  
   ```

1. 接続を切断し、 **[Connect automatically]\(自動的に接続する\)** チェック ボックスをオフにします。

   ![クリーンアップ](./media/vpn-gateway-howto-always-on-user-tunnel/disconnect.jpg)

## <a name="next-steps"></a>次のステップ

発生する可能性のある接続の問題をトラブルシューティングするには、「[Azure ポイント対サイト接続の問題](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)」を参照してください。
