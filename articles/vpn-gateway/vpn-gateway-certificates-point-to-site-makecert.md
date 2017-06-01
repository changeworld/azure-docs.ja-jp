---
title: "ポイント対サイトの証明書の作成とエクスポート: Makecert: Azure | Microsoft Docs"
description: "この記事では、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を Makecert を使用して行う手順について説明します。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: cherylmc
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 0800a7754241eb409dbd86db82b586a3e19a29fc
ms.contentlocale: ja-jp
ms.lasthandoff: 05/10/2017


---
# <a name="generate-and-export-certificates-for-point-to-site-connections-using-makecert"></a>Makecert を使用したポイント対サイト接続の証明書の生成とエクスポート

> [!NOTE]
> ポイント対サイト接続用の自己署名証明書を生成する Windows 10 コンピューターへのアクセス権がない場合にのみ、これらの手順を使用します。 Makecert は推奨されていません。 さらに Makecert は SHA-2 証明書を作成することはできず、(今なお P2S に対して有効な) SHA-1 のみを作成します。 これらの理由から、可能であれば [PowerShell の手順](vpn-gateway-certificates-point-to-site.md)を使用することをお勧めします。 PowerShell または Makecert のいずれかを使用して作成した証明書は、証明書の作成に使用したオペレーティング システムだけでなく、任意の[サポートされるクライアント オペレーティング システム](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq)にインストールできます。
>
>


この記事は、自己署名ルート証明書の作成、公開キーのエクスポート、クライアント証明書の生成を行う方法を説明します。 この記事には、ポイント対サイト構成の手順や、ポイント対サイトについてよく寄せられる質問は含まれません。 これらの情報については、次のリストにある「ポイント対サイトの構成」のいずれかの記事をご覧ください。

> [!div class="op_single_selector"]
> * [自己署名証明書の作成 - PowerShell](vpn-gateway-certificates-point-to-site.md)
> * [自己署名証明書の作成 - Makecert](vpn-gateway-certificates-point-to-site-makecert.md)
> * [ポイント対サイトの構成 - Resource Manager - Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [ポイント対サイトの構成 - リソース マネージャー - PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [ポイント対サイトの構成 - クラシック - Azure Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
> 
> 

ポイント対サイト接続では、認証に証明書を使用します。 ポイント対サイト接続を構成するときに、ルート証明書の公開キー (.cer ファイル) を Azure にアップロードする必要があります。 さらに、クライアント証明書は、ルート証明書から生成され、VNet に接続する各クライアント コンピューターにインストールされる必要があります。 クライアント証明書を使用してクライアントを認証できます。

## <a name="rootcert"></a>自己署名ルート証明書の作成

次の手順では、Makecert を使用して自己署名証明書を作成する方法を説明します。 これらの手順は、デプロイ モデル固有のものではありません。 リソース マネージャーとクラシックの両方で有効です。

1. [Makecert](https://msdn.microsoft.com/en-us/library/windows/desktop/aa386968(v=vs.85).aspx) をダウンロードし、インストールします。
2. インストール後、makecert.exe ユーティリティは 'C:\Program Files (x86)\Windows Kits\10\bin\<arch>' で見つかります。 管理者としてコマンド プロンプトを開き、makecert ユーティリティの場所に移動します。 以下の例を使用できます。

  ```cmd
  cd C:\Program Files (x86)\Windows Kits\10\bin\x64
  ```
3. コンピューターの個人証明書ストアで証明書を作成し、インストールします。 次の例は、P2S を構成するときに Azure にアップロードする、対応する *.cer* ファイルを作成します。 'P2SRootCert' と 'P2SRootCert.cer' を証明書に使う名前に置き換えます。 証明書は 'Current User\Personal\Certificates' にあります。

  ```cmd
  makecert -sky exchange -r -n "CN=P2SRootCert" -pe -a sha1 -len 2048 -ss My "P2SRootCert.cer"
  ```

## <a name="cer"></a>公開キー (.cer) のエクスポート

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]

エクスポートした .cer ファイルは Azure にアップロードする必要があります。 手順については、[ポイント対サイト接続の構成](vpn-gateway-howto-point-to-site-rm-ps.md#upload)に関するページをご覧ください。

### <a name="export-the-self-signed-certificate-and-private-key-to-store-it-optional"></a>自己署名証明書と証明書を保存するための秘密キーのエクスポート (省略可能)

自己署名ルート証明書をエクスポートし、安全に保管することもできます。 必要に応じて、後から別のコンピューターにインストールして、さらに多くのクライアント証明書を生成したり、別の .cer ファイルをエクスポートしたりできます。 自己署名ルート証明書を .pfx としてエクスポートするには、ルート証明書を選択し、「[クライアント証明書をエクスポートする](#clientexport)」と同じ手順を実行します。

## <a name="create-and-install-client-certificates"></a>クライアント証明書の作成とインストール

自己署名証明書をクライアント コンピューターに直接インストールすることはしません。 自己署名証明書からクライアント証明書を生成し、 生成したクライアント証明書をエクスポートして、クライアント コンピューターにインストールします。 これらの手順は、デプロイ モデル固有のものではありません。 リソース マネージャーとクラシックの両方で有効です。

### <a name="clientcert"></a>クライアント証明書の生成

ポイント対サイトで VNet に接続するすべてのクライアント コンピューターには、クライアント証明書がインストールされている必要があります。 自己署名ルート証明書からクライアント証明書を生成し、そのクライアント証明書をエクスポートしてインストールします。 クライアント証明書がインストールされていない場合は、認証が失敗します。 

次の手順では、自己署名ルート証明書からクライアント証明書を生成する方法を示しています。 同じルート証明書から複数のクライアント証明書を生成できます。 以下の手順を使用してクライアント証明書を生成すると、証明書の生成に使用したコンピューターにクライアント証明書が自動的にインストールされます。 クライアント証明書を別のクライアント コンピューターにインストールする場合は、その証明書をエクスポートできます。
 
1. 自己署名証明書の作成に使用した同じコンピューターで、管理者としてコマンド プロンプトを開きます。
2. クライアント証明書を生成するには、サンプルを変更して実行します。
  * *"P2SRootCert"* を、クライアント証明書を生成している自己署名ルートの名前に変更します。 必ずルート証明書の名前を使用してください。これは、自己署名ルートの作成時に指定した 'CN=' の値です。
  * *P2SChildCert* を、生成するクライアント証明書の名前に変更します。

  下の例を変更せずに実行すると、ルート証明書 P2SRootCert から生成された個人用証明書ストアに P2SChildcert という名前のクライアント証明書が作成されます。

  ```cmd
  makecert.exe -n "CN=P2SChildCert" -pe -sky exchange -m 96 -ss My -in "P2SRootCert" -is my -a sha1
  ```

### <a name="clientexport"></a>クライアント証明書のエクスポート

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]

### <a name="install"></a>エクスポートしたクライアント証明書のインストール

[!INCLUDE [Install client certificate](../../includes/vpn-gateway-certificates-install-client-cert-include.md)]

## <a name="next-steps"></a>次のステップ

引き続きポイント対サイト構成を使用します。 

* **Resource Manager** デプロイメント モデルの手順については、[VNet へのポイント対サイト接続の構成](vpn-gateway-howto-point-to-site-resource-manager-portal.md)に関する記事を参照してください。
* **クラシック** デプロイメント モデルの手順については、[VNet へのポイント対サイト VPN 接続の構成 (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md) に関する記事を参照してください。
