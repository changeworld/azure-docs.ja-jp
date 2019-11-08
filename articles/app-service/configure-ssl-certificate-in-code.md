---
title: アプリケーション コードで SSL 証明書を使用する - Azure App Service | Microsoft Docs
description: クライアント証明書を使用して、そのクライアント証明書を必要とするリモート リソースに接続する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/16/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1f042f72f82d2198472fe81670c697c0c4b28321
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510548"
---
# <a name="use-an-ssl-certificate-in-your-application-code-in-azure-app-service"></a>Azure App Service のアプリケーション コードに SSL 証明書を使用する

App Service のアプリ コードはクライアントとして機能し、証明書認証を必要とする外部サービスにアクセスする場合があります。 この攻略ガイドでは、アプリケーション コードで公開またはプライベートの証明書を使用する方法について説明します。

コードで証明書を使用するこの方法では、App Service の SSL 機能を利用します。そのため、お客様のアプリは **Basic** レベル以上でなければなりません。 または、[アプリ リポジトリに証明書ファイルを含める](#load-certificate-from-file)ことができます。しかし、これはプライベート証明書では推奨されません。

App Service の SSL 証明書の管理機能を使用すれば、証明書とアプリケーション コードを分離して管理し、機密データを保護できます。

## <a name="prerequisites"></a>前提条件

この攻略ガイドに従うには:

- [App Service アプリを作成する](/azure/app-service/)
- [証明書をアプリに追加する](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>拇印を確認する

<a href="https://portal.azure.com" target="_blank">Azure portal</a> の左側のメニューから、 **[App Services]**  >  **\<app-name>** を選択します。

アプリの左側のナビゲーションで **[TLS/SSL 設定]** を選択し、 **[秘密キー証明書 (.pfx)]** または **[公開キー証明書 (.cer)]** を選択します。

使用する証明書を見つけ、拇印をコピーします。

![証明書の拇印をコピーする](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="load-the-certificate"></a>証明書を読み込む

アプリ コードで証明書を使用するには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行して、`WEBSITE_LOAD_CERTIFICATES` アプリ設定に拇印を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

自分の証明書をすべてアクセス可能にするには、値を `*` に設定します。

> [!NOTE]
> この設定により、ほとんどの価格レベルでは指定された証明書が [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) ストアに配置されます。ただし、**Isolated** レベルの場合 (つまり、アプリが [App Service Environment](environment/intro.md) で実行される場合) は、証明書が [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) ストアに配置されます。
>

これで、構成された証明書をコードで使用する準備が整いました。

## <a name="load-the-certificate-in-code"></a>証明書をコードに読み込む

証明書にアクセスできるようになったら、証明書のサムプリントを使用して C# コードからアクセスします。 次のコードでは、サムプリントが `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` の証明書を読み込みます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser);
certStore.Open(OpenFlags.ReadOnly);
X509Certificate2Collection certCollection = certStore.Certificates.Find(
                            X509FindType.FindByThumbprint,
                            // Replace below with your certificate's thumbprint
                            "E661583E8FABEF4C0BEF694CBC41C28FB81CD870",
                            false);
// Get the first cert with the thumbprint
if (certCollection.Count > 0)
{
    X509Certificate2 cert = certCollection[0];
    // Use certificate
    Console.WriteLine(cert.FriendlyName);
}
certStore.Close();
...
```

<a name="file"></a>
## <a name="load-certificate-from-file"></a>ファイルから証明書を読み込む

自分のアプリケーション ディレクトリから証明書ファイルを読み込む必要がある場合、[Git](deploy-local-git.md) などは使用せず、[FTPS](deploy-ftp.md) を使用してアップロードすることをお勧めします。 プライベート証明書などの機密データは、ソース管理から分離しておく必要があります。

.NET コードに直接ファイルを読み込む場合でも、ライブラリでは現在のユーザー プロファイルが読み込まれるかどうかを確認します。 現在のユーザー プロファイルを読み込むには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを使用して、`WEBSITE_LOAD_USER_PROFILE` アプリ設定を構成します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
```

この設定が構成されると、次の C# の例では、アプリのリポジトリの `certs` ディレクトリから `mycert.pfx` という証明書が読み込まれます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
// Replace the parameter with "~/<relative-path-to-cert-file>".
string certPath = Server.MapPath("~/certs/mycert.pfx");

X509Certificate2 cert = GetCertificate(certPath, signatureBlob.Thumbprint);
...
```

## <a name="more-resources"></a>その他のリソース

* [SSL バインディングを使用してカスタム DNS 名のセキュリティを確保する](configure-ssl-bindings.md)
* [HTTPS の適用](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 の適用](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ:App Service 証明書](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
