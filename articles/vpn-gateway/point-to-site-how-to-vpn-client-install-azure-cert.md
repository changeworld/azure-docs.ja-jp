---
title: "P2S クライアント証明書のインストール | Azure"
description: "P2S 証明書認証用の Mac または Windows のクライアント証明書をインストールします。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2018
ms.author: cherylmc
ms.openlocfilehash: de98201b65f5531f334aded1056f622cecb6e190
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/14/2018
---
# <a name="install-a-client-certificate-for-point-to-site-azure-certificate-authentication-connections"></a>ポイント対サイトの Azure 証明書認証接続用のクライアント証明書のインストール

ポイント対サイトの Azure 証明書認証を使用して仮想ネットワークに接続するすべてのクライアントには、クライアント証明書が必要です。 この記事は、P2S を使って VNet に接続するときに認証に使用されるクライアント証明書をインストールする際に役立ちます。

## <a name="generate"></a>クライアント証明書の生成とエクスポート

クライアント証明書は、エンタープライズ CA ソリューションを使って生成されたルート証明書からも、自己署名ルート証明書からも生成することができます。 方法については、[PowerShell](vpn-gateway-certificates-point-to-site.md) または [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の手順を参照してください。 クライアント証明書を生成した後、それらを .pfx ファイルとしてエクスポートします。 エクスポートする際は、必ず証明書チェーン全体を含めるようにしてください。

## <a name="installwin"></a>Windows クライアントへの証明書のインストール

[!INCLUDE [Install on Windows](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="installmac"></a>Mac クライアントへの証明書のインストール

Mac VPN クライアントは、Resource Manager デプロイメント モデルのみでサポートされます。 クラシック デプロイメント モデルではサポートされません。

> [!NOTE]
>  IKEv2 は現在プレビューの段階です。
>

[!INCLUDE [Install on Mac](../../includes/vpn-gateway-certificates-install-mac-client-cert-include.md)]

## <a name="next-steps"></a>次の手順

ポイント対サイト構成の手順を続行します。

* [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
* [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
* [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)