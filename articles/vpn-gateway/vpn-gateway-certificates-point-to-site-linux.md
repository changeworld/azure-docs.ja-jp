---
title: 'ポイント対サイトの証明書の作成とエクスポート: Linux: CLI: Azure | Microsoft Docs'
description: Linux (strongSwan) CLI を使用して、自己署名ルート証明書を作成し、公開キーをエクスポートし、クライアント証明書を生成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305727"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Linux strongSwan CLI を使用してポイント対サイトの証明書を生成してエクスポートする

ポイント対サイト接続では、認証に証明書を使用します。 この記事では、Linux CLI と strongSwan を使用した自己署名ルート証明書の作成方法とクライアント証明書の生成方法について説明します。 別の証明書の手順が必要な場合は、[Powershell](vpn-gateway-certificates-point-to-site.md) または [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の記事を参照してください。

> [!NOTE]
> この記事の手順では strongSwan が必要です。
>

この記事の手順で使用されたコンピューター構成は次のとおりです。

| | |
|---|---|
|**コンピューター**| Ubuntu Server 16.04<br>ID_LIKE=debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
|**依存関係**| apt-get install strongswan-ikev2 strongswan-plugin-eap-tls<br>apt-get install libstrongswan-standard-plugins |

## <a name="install-strongswan"></a>strongSwan のインストール

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

GUI を使用して StrongSwan をインストールする方法については、「[クライアント構成](point-to-site-vpn-client-configuration-azure-cert.md#install)」の記事で手順を参照してください。

## <a name="generate-keys-and-certificate"></a>キーと証明書を生成する

1. キーと証明書を生成します。

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. CA 証明書 を base64 形式で印刷します。 これは Azure でサポートされている形式です。 P2S 構成の一環として、後にこれを Azure にアップロードすることになります。

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. ユーザー証明書を生成します。

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. ユーザー証明書を収納している p12 バンドルを生成します。 このバンドルは、[クライアント構成ファイル](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)と連携する場合に、次の手順で使用されます。

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>次の手順

ポイント対サイト構成を継続して、[VPN クライアント構成ファイルを作成してインストールします](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli)。