---
title: ポイント対サイトのクライアント証明書のインストール
titleSuffix: Azure VPN Gateway
description: P2S 証明書認証用のクライアント証明書のインストール方法 (Windows、Mac、Linux) について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2021
ms.author: cherylmc
ms.openlocfilehash: 917f60440d98924e5339f29fb99587eacf40b415
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/13/2021
ms.locfileid: "124766257"
---
# <a name="install-client-certificates-for-p2s-certificate-authentication-connections"></a>P2S 証明書認証接続用のクライアント証明書のインストール

P2S VPN ゲートウェイが証明書認証を要求するように構成されている場合、各クライアント コンピューターにはクライアント証明書がローカルにインストールされている必要があります。 この記事では、クライアント証明書をクライアント コンピューターにローカルでインストールする方法について説明します。 また、[Intune](/mem/intune/configuration/vpn-settings-configure) を使用して、特定の VPN クライアント プロファイルと証明書をインストールすることもできます。

自己署名ルート証明書からクライアント証明書を生成する場合は、次の記事のいずれかを参照してください。

* [証明書の生成 - PowerShell](vpn-gateway-certificates-point-to-site.md)
* [証明書の生成 - MakeCert](vpn-gateway-certificates-point-to-site-makecert.md)
* [証明書の生成 - Linux](vpn-gateway-certificates-point-to-site-linux.md) 

## <a name="windows"></a><a name="installwin"></a>Windows

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="mac"></a><a name="installmac"></a>Mac

>[!NOTE]
>Mac VPN クライアントは、[Resource Manager デプロイ モデル](../azure-resource-manager/management/deployment-models.md)のみでサポートされます。 クラシック デプロイ モデルではサポートされません。
>
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="linux"></a><a name="installlinux"></a>Linux

Linux のクライアント証明書は、クライアント構成の一部としてクライアントにインストールされています。 手順については、「[クライアント構成 - Linux](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)」を参照してください。

## <a name="next-steps"></a>次のステップ

ポイント対サイト構成の手順を続行し、[VPN クライアント構成ファイルを作成およびインストール](point-to-site-vpn-client-configuration-azure-cert.md)します。