---
title: 'ポイント対サイトの証明書を生成してエクスポートするLinux: CLI'
description: Linux (strongSwan) CLI を使用して、自己署名ルート証明書を作成し、公開キーをエクスポートし、クライアント証明書を生成します。
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 08/14/2019
ms.author: alzam
ms.openlocfilehash: 89f6014b548bd3dd66622d15149051e6b28e94b0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84984599"
---
# <a name="generate-and-export-certificates"></a>証明書を生成してエクスポートする

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、Linux CLI と strongSwan を使用した自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[Powershell](vpn-gateway-certificates-point-to-site.md) または [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。 CLI の代わりに GUI を使用して strongSwan をインストールする方法については、「[クライアント構成](point-to-site-vpn-client-configuration-azure-cert.md#install)」の記事で手順を参照してください。

## <a name="install-strongswan"></a>strongSwan のインストール

[!INCLUDE [strongSwan Install](../../includes/vpn-gateway-strongswan-install-include.md)]

## <a name="generate-and-export-certificates"></a>証明書を生成してエクスポートする

[!INCLUDE [strongSwan certificates](../../includes/vpn-gateway-strongswan-certificates-include.md)]

## <a name="next-steps"></a>次のステップ

ポイント対サイト構成を継続して、[VPN クライアント構成ファイルを作成してインストールします](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。
