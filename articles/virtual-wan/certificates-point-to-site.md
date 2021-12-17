---
title: ユーザー VPN 接続の証明書を生成してエクスポートする | Azure Virtual WAN
description: PowerShell を使用し、Virtual WAN ユーザー VPN (ポイント対サイト) 接続のために、自己署名付きのルート証明書を作成し、公開キーをエクスポートし、クライアント証明書を生成する方法について説明します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 04/27/2021
ms.author: cherylmc
ms.openlocfilehash: d126f68ed1eebae9dda7ad957748cb3258b5ad89
ms.sourcegitcommit: 62e800ec1306c45e2d8310c40da5873f7945c657
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2021
ms.locfileid: "108165301"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>ユーザー VPN 接続の証明書を生成してエクスポートする

ユーザー VPN (ポイント対サイト) 接続では、認証に証明書を使用します。 この記事では、Windows 10 または Windows Server 2016 で PowerShell を使用した、自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。

この記事の手順は、Windows 10 または Windows Server 2016 を実行するコンピューターで実行する必要があります。 証明書の生成に使用する PowerShell コマンドレットは、オペレーティング システムの一部であり、その他のバージョンの Windows では機能しません。 Windows 10 または Windows Server 2016 コンピューターは、証明書の生成にのみ必要です。 証明書が生成されたら、それらをアップロードしたり、サポートされる任意のクライアント オペレーティング システムにインストールしたりできます。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>次のステップ

[ユーザー VPN 接続の Virtual WAN の手順](virtual-wan-about.md)を続行する
