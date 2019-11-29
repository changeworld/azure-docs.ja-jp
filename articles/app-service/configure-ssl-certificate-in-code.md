---
title: コードで SSL 証明書を使用する - Azure App Service | Microsoft Docs
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
ms.date: 11/04/2019
ms.author: cephalin
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 1546ded1977e1e26792189e1d992d106d3d77ef2
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/21/2019
ms.locfileid: "74271278"
---
# <a name="use-an-ssl-certificate-in-your-code-in-azure-app-service"></a>Azure App Service のコード内から SSL 証明書を使用する

[App Service に追加したパブリック証明書またはプライベート証明書](configure-ssl-certificate.md)には、アプリケーション コード内からアクセスすることができます。 アプリ コードはクライアントとして、証明書認証を必要とする外部サービスにアクセスすることがあるほか、暗号タスクを実行しなければならない場合もあります。 この攻略ガイドでは、アプリケーション コードで公開またはプライベートの証明書を使用する方法について説明します。

コードで証明書を使用するこの方法では、App Service の SSL 機能を利用します。そのため、お客様のアプリは **Basic** レベル以上でなければなりません。 アプリが **Free** レベルまたは **Shared** レベルの場合は、[アプリのリポジトリに証明書ファイルを格納](#load-certificate-from-file)することができます。

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

## <a name="make-the-certificate-accessible"></a>証明書をアクセス可能にする

アプリ コードで証明書にアクセスするには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> で次のコマンドを実行して、`WEBSITE_LOAD_CERTIFICATES` アプリ設定に拇印を追加します。

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

自分の証明書をすべてアクセス可能にするには、値を `*` に設定します。

## <a name="load-certificate-in-windows-apps"></a>Windows アプリで証明書を読み込む

Windows ホステッド アプリは、`WEBSITE_LOAD_CERTIFICATES` アプリ設定によって、Windows 証明書ストア内の指定された証明書にアクセスできるようになります。その場所は、[価格レベル](overview-hosting-plans.md)によって異なります。

- **Isolated** レベル - [Local Machine\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 内。 
- その他すべてのレベル - [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores) 内。

C# コードで証明書にアクセスするには、証明書の拇印を使用します。 次のコードでは、サムプリントが `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` の証明書を読み込みます。

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

Java コードで "Windows-MY" ストアの証明書にアクセスするには、[サブジェクトの共通名] フィールドを使用します (「[公開鍵証明書](https://en.wikipedia.org/wiki/Public_key_certificate)」を参照)。 次のコードは、秘密キー証明書を読み込む方法を示しています。

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

Windows 証明書ストアがサポートされない言語またはサポートが不十分な言語については、「[ファイルから証明書を読み込む](#load-certificate-from-file)」を参照してください。

## <a name="load-certificate-in-linux-apps"></a>Linux アプリで証明書を読み込む

Linux ホステッド アプリ (カスタム コンテナー アプリを含む) は、`WEBSITE_LOAD_CERTIFICATES` アプリ設定によって、指定された証明書にファイルとしてアクセスできるようになります。 それらのファイルは、次のディレクトリに格納されます。

- プライベート証明書 - `/var/ssl/private` ( `.p12` ファイル)
- パブリック証明書 - `/var/ssl/certs` ( `.der` ファイル)

証明書のファイル名は、証明書の拇印です。 次の C# コードは、Linux アプリでパブリック証明書を読み込む方法を示しています。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js、PHP、Python、Java、Ruby で SSL 証明書をファイルから読み込む方法については、それぞれの言語または Web プラットフォームのドキュメントを参照してください。

## <a name="load-certificate-from-file"></a>ファイルから証明書を読み込む

読み込む必要のある証明書ファイルを手動でアップロードする場合は、[Git](deploy-local-git.md) などではなく [FTPS](deploy-ftp.md) を使用して証明書をアップロードすることをお勧めします。 プライベート証明書などの機密データは、ソース管理から分離しておく必要があります。

> [!NOTE]
> Windows 上の ASP.NET および ASP.NET Core は、証明書をファイルから読み込む場合であっても、証明書ストアにアクセスする必要があります。 Windows .NET アプリで証明書ファイルを読み込むには、<a target="_blank" href="https://shell.azure.com" >Cloud Shell</a> から次のコマンドを使用して、現在のユーザー プロファイルを読み込みます。
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> コードで証明書を使用するこの方法では、App Service の SSL 機能を利用します。そのため、お客様のアプリは **Basic** レベル以上でなければなりません。

次の C# サンプルでは、アプリ内で相対パスからパブリック証明書を読み込みます。

```csharp
using System;
using System.Security.Cryptography.X509Certificates;

...
var bytes = System.IO.File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js、PHP、Python、Java、Ruby で SSL 証明書をファイルから読み込む方法については、それぞれの言語または Web プラットフォームのドキュメントを参照してください。

## <a name="more-resources"></a>その他のリソース

* [SSL バインディングを使用してカスタム DNS 名のセキュリティを確保する](configure-ssl-bindings.md)
* [HTTPS の適用](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 の適用](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ:App Service 証明書](https://docs.microsoft.com/azure/app-service/faq-configuration-and-management/)
