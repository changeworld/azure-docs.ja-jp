---
title: Azure Virtual WAN ユーザー VPN 接続の証明書を生成してエクスポートする |Microsoft Docs
description: Windows 10 または Windows Server 2016 で PowerShell を使用して、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を行います。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: cherylmc
ms.openlocfilehash: 57d730a92c687a297a35b8cd6cccd955025694af
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510924"
---
# <a name="generate-and-export-certificates-for-virtual-wan-user-vpn-connections"></a>Virtual WAN ユーザー VPN 接続の証明書を生成してエクスポートする

ユーザー VPN 接続では、認証に証明書を使用します。 この記事では、Windows 10 または Windows Server 2016 で PowerShell を使用した、自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。

この記事の手順は、Windows 10 または Windows Server 2016 を実行するコンピューターで実行する必要があります。 証明書の生成に使用する PowerShell コマンドレットは、オペレーティング システムの一部であり、その他のバージョンの Windows では機能しません。 Windows 10 または Windows Server 2016 コンピューターは、証明書の生成にのみ必要です。 証明書が生成されたら、それらをアップロードしたり、サポートされる任意のクライアント オペレーティング システムにインストールしたりできます。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>次の手順

[ユーザー VPN 接続の Virtual WAN の手順](virtual-wan-about.md)を続行する
