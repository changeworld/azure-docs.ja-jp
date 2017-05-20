---
title: "既存のカスタム SSL 証明書の Azure Web Apps へのバインド | Microsoft Docs"
description: "Azure App Service で、Web アプリ、モバイル アプリのバックエンド、または API アプリにカスタム SSL 証明書をバインドする方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: 5d5bf588-b0bb-4c6d-8840-1b609cfb5750
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: a0e245121f2a9ff4109b281cd7286ed601bf64ac
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017


---
# <a name="bind-an-existing-custom-ssl-certificate-to-azure-web-apps"></a>既存のカスタム SSL 証明書の Azure Web Apps へのバインド

このチュートリアルでは、信頼された証明機関から購入したカスタム SSL 証明書を [Azure Web Apps](app-service-web-overview.md) にバインドする方法について説明します。 完了したら、カスタムの DNS ドメインの HTTPS エンドポイントで、Web アプリにアクセスできるようになります。

![カスタム SSL 証明書付きの Web アプリ](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム SSL 証明書を App Service にバインドする
> * アプリに HTTPS を適用する
> * スクリプトで SSL 証明書のバインドを自動化する

> [!TIP]
> カスタム SSL 証明書を取得する必要がある場合、Azure Portal から直接取得して Web アプリにバインドすることができます。 [App Service 証明書のチュートリアル](web-sites-purchase-ssl-web-site.md)に従ってください。 
>
> 

## <a name="prerequisites"></a>前提条件
このチュートリアルの内容に従う前に、次の作業が完了していることを確認してください。

- [App Service アプリを作成する](/azure/app-service/)
- [カスタム DNS 名を Web アプリにマップする](web-sites-custom-domain-name.md)
- 信頼された証明機関から SSL 証明書を取得する

<a name="requirements"></a>

### <a name="requirements-for-your-ssl-certificate"></a>SSL 証明書の必要条件

App Service で証明書を使用するには、次のすべての要件を証明書が満たしている必要があります。

* 信頼された証明機関によって署名されている
* パスワードで保護された PFX ファイルとしてエクスポートされている
* 少なくとも 2048 ビット長の秘密キーが含まれている
* 証明書チェーン内のすべての中間証明書が含まれている

> [!NOTE]
> **楕円曲線暗号 (ECC) 証明書** は、App Service で使用できますが、この記事では説明しません。 ECC 証明書を作成する正確な手順については、証明機関にお問い合わせください。
> 
>

## <a name="prepare-your-web-app"></a>Web アプリの準備
カスタム SSL 証明書を Web アプリにバインドするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Basic**、**Standard** または **Premium** のいずれかのレベルである必要があります。 この手順では、Web アプリが、サポートされている価格レベルであることを確認します。

### <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal を開きます。 そのためには、Azure アカウントで [https://portal.azure.com](https://portal.azure.com) にサインインします。

### <a name="navigate-to-your-web-app"></a>Web アプリに移動する
左側のメニューで **[App Services]** をクリックした後、Web アプリの名前をクリックします。

![Web アプリの選択](./media/app-service-web-tutorial-custom-ssl/select-app.png)

Web アプリの管理ブレードが表示されます (_ブレード_: 水平方向に開かれるポータル ページ)。  

### <a name="check-the-pricing-tier"></a>価格レベルの確認

Web アプリのブレードの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service のプラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-ssl/scale-up-menu.png)

Web アプリが **Free** レベルまたは **Shared** レベルに含まれていないことを確認します。 Web アプリの現在のレベルは、ダーク ブルーのボックスで強調表示されます。 

![価格レベルの確認](./media/app-service-web-tutorial-custom-ssl/check-pricing-tier.png)

カスタム SSL は、**Free** レベルと **Shared** レベルではサポートされていません。 スケール アップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、**[価格レベルの選択]** ブレードを閉じて、[SSL 証明書のアップロードとバインド](#upload)に関するセクションにスキップします。

### <a name="scale-up-your-app-service-plan"></a>App Service プランのスケール アップ

**Basic**、**Standard**、**Premium** のいずれかのレベルを選択します。 

**[選択]**をクリックします。

![価格レベルの選択](./media/app-service-web-tutorial-custom-ssl/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール アップの通知](./media/app-service-web-tutorial-custom-ssl/scale-notification.png)

<a name="upload"></a>

## <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

Web アプリに、SSL 証明書をアップロードする準備ができました。 

### <a name="export-certificate-to-pfx"></a>PFX への証明書のエクスポート

カスタム SSL 証明書は、証明書の要求と一緒に生成された秘密キーと共にエクスポートする必要があります。

証明書の要求の生成に OpenSSL を使用した場合、秘密キーは作成されています。 証明書を PFX をエクスポートするには、次のコマンドを実行します。

```bash
openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
```

IIS または _Certreq.exe_ を使用して証明書の要求を生成した場合、まず、ローカルのコンピューターに証明書をインストールします。その後、「[証明書を秘密キーと共にエクスポートする](https://technet.microsoft.com/library/cc754329(v=ws.11).aspx)」に記載されている手順に従って、PFX にエクスポートします。

### <a name="upload-your-ssl-certificate"></a>SSL 証明書のアップロード

SSL 証明書をアップロードするには、Web アプリの左側のナビゲーションで **[SSL 証明書]** をクリックします。

**[証明書のアップロード]** をクリックします。

**[PFX 証明書ファイル]** で、PFX ファイルを選択します。 **[証明書のパスワード]** で、PFX ファイルのエクスポート時に作成したパスワードを入力します。

**[アップロード]**をクリックします。

![証明書のアップロード](./media/app-service-web-tutorial-custom-ssl/upload-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL 証明書]** ページにアップロードした証明書が表示されます。

![アップロードされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-uploaded.png)

### <a name="bind-your-ssl-certificate"></a>SSL 証明書のバインド

アップロードした証明書が **[SSL 証明書]** ページに表示されています。

**[SSL バインディング]** セクションで、**[Add bindings] (バインドの追加)** をクリックします。

**[Add SSL Binding] (SSL バインドの追加)** ブレードで、ドロップダウンから保護するドメインの名前と使用する証明書を選択します。 

**[SSL Type] (SSL の種類)** で、**[Server Name Indication (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ベースの SSL を使用するか IP ベースの SSL を使用するかを選択します。
   
- **SNI ベースの SSL** - SNI ベースの SSL バインドを複数追加することができます。 このオプションでは、複数の SSL 証明書を使用して、同一の IP アドレス上の複数のドメインを保護できます。 最新のブラウザーのほとんど (Inernet Explorer、Chrome、Firefox、Opera など) が SNI をサポートしています (ブラウザーのサポートに関するより包括的な情報については、「[Server Name Indication](http://wikipedia.org/wiki/Server_Name_Indication)」を参照してください)。
- **IP ベースの SSL** - IP ベースの SSL バインドを 1 つだけ追加することができます。 このオプションでは、SSL 証明書を 1 つだけ使用して、専用のパブリック IP アドレスを保護します。 複数のドメインを保護するには、同じ SSL 証明書を使用してすべてのドメインを保護する必要があります。 これは、SSL バインドの従来のオプションです。 

**[Add Binding] (バインドの追加)** をクリックします。

![SSL 証明書のバインド](./media/app-service-web-tutorial-custom-ssl/bind-certificate.png)

App Service による証明書のアップロードが完了すると、**[SSL バインド]** セクションにアップロードした証明書が表示されます。

![Web アプリにバインドされた証明書](./media/app-service-web-tutorial-custom-ssl/certificate-bound.png)

## <a name="remap-a-record-for-ip-ssl"></a>IP SSL の A レコードの再マップ

Web アプリで IP ベースの SSL を使用していない場合、[カスタム ドメインの HTTPS のテスト](#test)に関するセクションにスキップしてください。 

既定では、Web アプリは、共有のパブリック IP アドレスを使用します。 IP ベースの SSL で証明書をバインドすると、Web アプリ用の新規の専用 IP アドレスが App Service によってすぐに作成されます。

Web アプリに A レコードをマップした場合は、この新規の専用 IP アドレスでドメイン レジストリを更新します。

Web アプリの **[カスタム ドメイン]** ページが、新規の専用 IP アドレスで更新されます。 [この IP アドレスをコピー](app-service-web-tutorial-custom-domain.md#info)して、この新しい IP アドレスに [A レコードを再マップ](app-service-web-tutorial-custom-domain.md#create-a)します。

<a name="test"></a>

## <a name="test-https"></a>HTTPS のテスト
この時点で残っている作業は、HTTPS がカスタム ドメインで機能するかどうかを確認することだけです。 さまざまなブラウザーで `https://<your.custom.domain>` にアクセスし、Web アプリの要求を処理できることを確認します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-ssl/app-with-custom-ssl.png)

> [!NOTE]
> Web アプリで証明書検証エラーが返された場合は、自己署名証明書を使用している可能性があります。
>
> そうでない場合は、証明書を PFX ファイルにエクスポートするときに中間証明書を含めなかった可能性があります。 
>
>

<a name="bkmk_enforce"></a>

## <a name="enforce-https"></a>HTTPS の適用
HTTP による Web アプリへのアクセスを許可する場合は、この手順をスキップしてください。 

App Service では HTTPS の使用が強制されないため、どなたでも引き続き HTTP を使用してアプリにアクセスできます**。 Web アプリに HTTPS を強制するために、Web アプリの _web.config_ ファイルで書き換え規則を定義することができます。 App Service は、Web アプリの言語フレームワークに関係なく、このファイルを使用します。

> [!NOTE]
> 言語に固有の要求のリダイレクトがあります。 ASP.NET MVC では、_web.config_ 内の書き換え規則の代わりに [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) フィルターを使用できます ([セキュリティで保護された ASP.NET MVC 5 アプリを Web アプリにデプロイする方法](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md)に関するページを参照してください)。
> 
> 

.NET 開発者の方は、比較的このファイルに馴染みがあると思います。 このファイルは、ソリューションのルートにあります。

また、PHP、Node.js、Python または Java で開発する場合は、お客様の代わりに App Service でこのファイルを生成することもできます。

「[FTP/S を使用した Azure App Service へのアプリのデプロイ](app-service-deploy-ftp.md)」の指示に従って、Web アプリの FTP エンドポイントに接続します。 

このファイルは、_/home/site/wwwroot_ にあります。 ない場合は、次の XML を含む _web.config_ をこのフォルダーに作成します。

```xml   
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <!-- BEGIN rule TAG FOR HTTPS REDIRECT -->
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
        <!-- END rule TAG FOR HTTPS REDIRECT -->
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```

既存の _web.config_ の場合は、`<rule>` タグ全体を _web.config_ の `configuration/system.webServer/rewrite/rules` 要素にコピーするだけで済みます。 他の `<rule>` タグが _web.config_ 内にある場合は、コピーした `<rule>` タグを他の `<rule>` タグの前に配置します。

この規則は、ユーザーが HTTP を Web アプリに要求したときに、HTTPS プロトコルに HTTP 301 (永続的リダイレクト) を返します。 たとえば、`http://contoso.com` から `https://contoso.com` にリダイレクトします。

IIS URL 書き換えモジュールの詳細については、 [URL 書き換え](http://www.iis.net/downloads/microsoft/url-rewrite) のドキュメントを参照してください。

## <a name="automate-with-scripts"></a>スクリプトによる自動化

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、Web アプリの SSL バインドをスクリプトで自動化することができます。

### <a name="azure-cli"></a>Azure CLI

次のコマンドは、エクスポートした PFX ファイルをアップロードし、拇印を取得します。 

```bash
thumprint=$(az appservice web config ssl upload \
    --name <app_name> \
    --resource-group <resource_group_name> \
    --certificate-file <path_to_PFX_file> \
    --certificate-password <PFX_password> \
    --query thumbprint \
    --output tsv)
```

次のコマンドは、直前のコマンドで取得した拇印を使用して、SNI ベースの SSL バインドを追加します。

```bash
az appservice web config ssl bind \
    --name <app_name> \
    --resource-group <resource_group_name>
    --certificate-thumbprint $thumbprint \
    --ssl-type SNI \
```

### <a name="azure-powershell"></a>Azure PowerShell

次のコマンドは、エクスポートした PFX ファイルをアップロードし、SNI ベースの SSL バインドを追加します。

```PowerShell
New-AzureRmWebAppSSLBinding `
    -WebAppName <app_name> `
    -ResourceGroupName <resource_group_name> `
    -Name <dns_name> `
    -CertificateFilePath <path_to_PFX_file> `
    -CertificatePassword <PFX_password> `
    -SslState SniEnabled
```
## <a name="what-you-have-learned"></a>学習した内容

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * アプリの価格レベルをアップグレードする
> * カスタム SSL 証明書を App Service にバインドする
> * アプリに HTTPS を適用する
> * スクリプトで SSL 証明書のバインドを自動化する

