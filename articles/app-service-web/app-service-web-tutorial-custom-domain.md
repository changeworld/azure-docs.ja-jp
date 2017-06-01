---
title: "既存のカスタム DNS 名を Azure Web Apps にマップする | Microsoft Docs"
description: "既存のカスタム DNS ドメイン名 (バニティ ドメイン) を、Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリに追加する方法について説明します。"
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: 
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 000440fb2c38eadc0ffdcab84a3c23bb034e834f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/09/2017

---
# <a name="map-an-existing-custom-dns-name-to-azure-web-apps"></a>既存のカスタム DNS 名を Azure Web Apps にマップする

このチュートリアルでは、既存のカスタム DNS 名を [Azure Web Apps](app-service-web-overview.md) にマップする方法について説明します。 

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメイン (たとえば `www.contoso.com`) をマップする
> * A レコードを使用してルート ドメイン (たとえば `contoso.com`) をマップする
> * CNAME レコードを使用してワイルドカード ドメイン (たとえば `*.contoso.com`) をマップする
> * スクリプトでドメイン マッピングを自動化する

**CNAME レコード**または **A レコード**のいずれかを使用して、カスタム DNS 名を App Service にマップします。

> [!NOTE]
> ルート ドメイン (`contoso.com` など) を除くすべてのカスタム DNS 名に CNAME を使用することをお勧めします。 
> 
> 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、ドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権と、ドメインを編集するためのアクセス許可が必要です。 

たとえば、`contoso.com` と `www.contoso.com` の DNS エントリを追加するには、`contoso.com` ルート ドメインに対して DNS 設定を構成するためのアクセス権が必要です。 

> [!NOTE]
> 既存のドメイン名がない場合は、[App Service ドメインのチュートリアル](custom-dns-web-site-buydomains-web-app.md)に従って、Azure Portal を使用してドメインを購入することを検討してください。 
>
>

## <a name="prepare-your-app"></a>アプリケーションの準備
カスタム DNS 名をマップするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が有料レベル (**Shared**、**Basic**、**Standard**、または **Premium**) である必要があります。 この手順では、App Service アプリがサポートされている価格レベルであることを確認します。

### <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal を開きます。 そのためには、Azure アカウントで [https://portal.azure.com](https://portal.azure.com) にサインインします。

### <a name="navigate-to-your-app"></a>アプリに移動する
左側のメニューで **[App Services]** をクリックした後、アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

App Service アプリの管理ブレードが表示されます (_ブレード_: 水平方向に開かれるポータル ページ)。  

### <a name="check-the-pricing-tier"></a>価格レベルを確認する

アプリ ブレードの左側のナビゲーションで **[設定]** セクションまでスクロールし、**[スケール アップ (App Service プラン)]** を選択します。

![スケール アップ メニュー](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

アプリが **Free** レベルに含まれていないことを確認します。 アプリの現在のレベルは、ダーク ブルーのボックスで強調表示されます。 

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

カスタム DNS は、**Free** レベルではサポートされていません。 スケールアップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、**[価格レベルの選択]** ブレードを閉じて、[[Map a CNAME record (CNAME レコードをマップ)]](#cname) または [[Map an A record (A レコードをマップ)]](#a) に進みます。

### <a name="scale-up-your-app-service-plan"></a>App Service プランをスケールアップする

Free 以外のレベル (**Shared**、**Basic**、**Standard**、または **Premium**) を選択します。 

**[選択]**をクリックします。

![価格レベルの確認](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![スケール操作の確認](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="cname"></a>

## <a name="map-a-cname-record"></a>CNAME レコードのマップ

この例では、`www` サブドメイン (`www.contoso.com`) の CNAME レコードを追加します。 

### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

まず、ドメイン プロバイダーの Web サイトにサインインします。

DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、ご使用のプロバイダーのドキュメントを参照してください。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 

通常、リンクを見つけるには、アカウント情報を表示し、 **[ドメイン]**などのリンクを探します。 次に、DNS レコードを管理するためのリンクを探します。 このリンクには、**[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** という名前が付けられている場合があります。

以下のスクリーンショットは、DNS レコード ページの例です。

![DNS レコード ページの例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

このスクリーンショットの例では、**[追加]** をクリックしてレコードを作成します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 この場合も、プロバイダーのドキュメントを参照してください。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクをクリックするまで DNS レコードの変更が反映されません。 
>
>

### <a name="create-the-cname-record"></a>CNAME レコードを作成する

サブドメインをアプリの既定のホスト名 (`<app_name>.azurewebsites.net`) にマップするための CNAME レコードを追加します。

`www.contoso.com` ドメインの場合、CNAME レコードでは、`www` という名前が `<app_name>.azurewebsites.net` を指定する必要があります。

DNS レコード ページは、次のスクリーンショットのようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>アプリの CNAME レコード マッピングを有効にする

構成した DNS 名をアプリに追加する準備ができました。

アプリ ブレードの左側のナビゲーションで、**[カスタム ドメイン]** をクリックします。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

アプリの **[カスタム ドメイン]** ブレードで、完全修飾カスタム DNS 名 (`www.contoso.com`) を一覧に追加する必要があります。

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

先ほど CNAME レコードを構成した完全修飾ドメイン名 (`www.contoso.com` など) を入力し、**[検証]** をクリックします。

それ以外の場合は、**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[CNAME レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

<a name="a"></a>

## <a name="map-an-a-record"></a>A レコードのマップ

この例では、ルート ドメイン `contoso.com` の A レコードを追加します。 

<a name="info"></a>

### <a name="copy-your-apps-ip-address"></a>アプリの IP アドレスをコピーする

A レコードをマップするには、アプリの外部 IP アドレスが必要です。 この IP アドレスは、**[カスタム ドメイン]** ブレードで見つけることができます。

アプリ ブレードの左側のナビゲーションで、**[カスタム ドメイン]** をクリックします。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[カスタム ドメイン]** ページで、アプリの IP アドレスをコピーします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

まず、ドメイン プロバイダーの Web サイトにサインインします。

DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、ご使用のプロバイダーのドキュメントを参照してください。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 

通常、リンクを見つけるには、アカウント情報を表示し、 **[ドメイン]**などのリンクを探します。 次に、DNS レコードを管理するためのリンクを探します。 このリンクには、**[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** という名前が付けられている場合があります。

以下のスクリーンショットは、DNS レコード ページの例です。

![DNS レコード ページの例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

このスクリーンショットの例では、**[追加]** をクリックしてレコードを作成します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 この場合も、プロバイダーのドキュメントを参照してください。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクをクリックするまで DNS レコードの変更が反映されません。 
>
>

<a name="create-a"></a>

### <a name="create-the-a-record"></a>A レコードを作成する

A レコードをアプリにマップする場合、App Service では実際には次の **2 つ**の DNS レコードが必要になります。

- アプリの IP アドレスにマップするための **A** レコード。
- アプリの既定のホスト名 `<app_name>.azurewebsites.net` にマップするための **TXT** レコード。 このレコードにより、App Service はマップするカスタム ドメインの所有者を確認できます。

`www.contoso.com` ドメインの場合、次の表に従って A および TXT レコードを作成します (`@` は、通常、ルート ドメインを表します)。 

| レコード タイプ | Host | 値 |
| - | - | - |
| A | `@` | 「[アプリの IP アドレスをコピーする](#info)」で取得した IP アドレス |
| TXT | `@` | `<app_name>.azurewebsites.net` |

DNS レコード ページは、次のスクリーンショットのようになります。

![DNS レコード ページ](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="enable-a"></a>

### <a name="enable-the-a-record-mapping-in-your-app"></a>アプリの A レコード マッピングを有効にする

構成した DNS 名をアプリに追加する準備ができました。

Azure Portal のアプリの **[カスタム ドメイン]** ページに戻り、完全修飾カスタム DNS 名 (`contoso.com`) を一覧に追加する必要があります。

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

先ほど A レコードを構成した完全修飾ドメイン名 (`contoso.com` など) を入力し、**[検証]** をクリックします。

それ以外の場合は、**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![追加された A レコード](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![検証エラー](./media/app-service-web-tutorial-custom-domain/verification-error.png)

<a name="wildcard"></a>

## <a name="map-a-wildcard-domain"></a>ワイルドカード ドメインのマップ

[ワイルドカード DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (`*.contoso.com` など) を App Service アプリにマップすることもできます。 

次の手順では、CNAME レコードを使用して、ワイルドカード ドメイン `*.contoso.com` をマップする方法を示します。 

### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

まず、ドメイン プロバイダーの Web サイトにサインインします。

DNS レコードの管理ページを探します。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、ご使用のプロバイダーのドキュメントを参照してください。 **[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 

通常、リンクを見つけるには、アカウント情報を表示し、 **[ドメイン]**などのリンクを探します。 次に、DNS レコードを管理するためのリンクを探します。 このリンクには、**[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** という名前が付けられている場合があります。

以下のスクリーンショットは、DNS レコード ページの例です。

![DNS レコード ページの例](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

このスクリーンショットの例では、**[追加]** をクリックしてレコードを作成します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 この場合も、プロバイダーのドキュメントを参照してください。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクをクリックするまで DNS レコードの変更が反映されません。 
>
>

### <a name="create-the-cname-record"></a>CNAME レコードを作成する

ワイルドカード名をアプリの既定のホスト名 (`<app_name>.azurewebsites.net`) にマップするための CNAME レコードを追加します。

`*.contoso.com` ドメインの場合、CNAME レコードでは、`*` という名前が `<app_name>.azurewebsites.net` を指定する必要があります。

DNS レコード ページは、次のスクリーンショットのようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record.png)

### <a name="enable-the-cname-record-mapping-in-your-app"></a>アプリの CNAME レコード マッピングを有効にする

ワイルドカード名と一致するすべてのサブドメインを追加できるようになりました。

`*.contoso.com` ワイルドカードの場合は、`sub1.contoso.com` と `sub2.contoso.com` を追加できます。 

アプリ ブレードの左側のナビゲーションで、**[カスタム ドメイン]** をクリックします。 

![[カスタム ドメイン] メニュー](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![ホスト名の追加](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

ワイルドカード ドメインと一致するサブドメインの完全修飾ドメイン名 (`sub1.contoso.com` など) を入力し、**[検証]** をクリックします。

それ以外の場合は、**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[CNAME レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![アプリへの DNS 名の追加](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

**+** アイコンをもう一度クリックすると、ワイルドカード ドメインと一致する別のホスト名を追加できます。

たとえば、上記の手順を使用して `sub2.contoso.com` を追加します。

![追加された CNAME レコード](./media/app-service-web-tutorial-custom-domain/cname-record-added-wildcard2.png)

## <a name="test-in-browser"></a>ブラウザーでテストする

ブラウザーで、構成済みの DNS 名 (`contoso.com` と `www.contoso.com`) を参照します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/overview) を使用すると、カスタム ドメインの管理をスクリプトで自動化できます。 

### <a name="azure-cli"></a>Azure CLI 

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。 

```bash 
az appservice web config hostname add \
    --webapp <app_name> \
    --resource-group <resourece_group_name> \ 
    --name <fully_qualified_domain_name> 
``` 

詳細については、「[カスタム ドメインを Web アプリにマップする](scripts/app-service-cli-configure-custom-domain.md)」を参照してください 

### <a name="azure-powershell"></a>Azure PowerShell 

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。 

```PowerShell  
Set-AzureRmWebApp `
    -Name <app_name> `
    -ResourceGroupName <resourece_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app_name>.azurewebsites.net") 
```

詳細については、「[カスタム ドメインを Web アプリに割り当てる](scripts/app-service-powershell-configure-custom-domain.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * CNAME レコードを使用してサブドメインをマップする
> * A レコードを使用してルート ドメインをマップする
> * CNAME レコードを使用してワイルドカード ドメインをマップする
> * スクリプトでドメイン マッピングを自動化する

次のチュートリアルに進み、カスタム SSL 証明書をバインドする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム SSL 証明書の Azure Web Apps へのバインド](app-service-web-tutorial-custom-ssl.md)

