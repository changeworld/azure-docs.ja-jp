---
title: ポイント対サイトのクライアント証明書のインストール
titleSuffix: Azure VPN Gateway
description: P2S 証明書認証用のクライアント証明書のインストール方法 (Windows、Mac、Linux) について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 06/03/2021
ms.author: cherylmc
ms.openlocfilehash: d4ab1009b38d250a39455a9e8a470cd7f1156793
ms.sourcegitcommit: 70ce9237435df04b03dd0f739f23d34930059fef
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/05/2021
ms.locfileid: "111527112"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 証明書認証接続用のクライアント証明書のインストール

P2S VPN ゲートウェイが証明書認証を要求するように構成されている場合、各クライアントにはクライアント証明書がローカルにインストールされている必要があります。 クライアント証明書は、自己署名ルート証明書からも、エンタープライズ CA ソリューションを使って生成されたルート証明書からも生成することができます。 

クライアント証明書を生成すると、通常、証明書は生成されたクライアント コンピューターに自動的にインストールされます。 別のクライアント コンピューターから VNet に接続する場合は、接続するコンピューターにクライアント証明書をインストールする必要があります。 これは、そのコンピューター上の VPN クライアントの構成に加えて行います。

複数の方法を使用して、自己署名証明書を生成およびエクスポートできます。 詳細については、次の記事を参照してください。

* [PowerShell](vpn-gateway-certificates-point-to-site.md)
* [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN クライアントは、Resource Manager デプロイ モデルのみでサポートされます。 クラシック デプロイ モデルではサポートされません。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux のクライアント証明書は、クライアント構成の一部としてクライアントにインストールされています。 手順については、「[クライアント構成 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)」を参照してください。

## <a name="next-steps"></a>次のステップ

ポイント対サイト構成の手順を続行し、[VPN クライアント構成ファイルを作成およびインストール](point-to-site-vpn-client-configuration-azure-cert.md)します。
