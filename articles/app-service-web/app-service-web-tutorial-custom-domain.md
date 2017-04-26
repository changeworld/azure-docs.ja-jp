---
title: "カスタム DNS 名を Azure Web アプリにマップする | Microsoft Docs"
description: "Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリにカスタム DNS ドメイン名 (バニティ ドメイン) を追加する方法について説明します。"
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
ms.date: 03/29/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 26d460a699e31f6c19e3b282fa589ed07ce4a068
ms.openlocfilehash: 25ddbd0c89ec7eee8eb240cf111245bb8b5add48
ms.lasthandoff: 04/04/2017


---
# <a name="map-a-custom-dns-name-to-an-azure-web-app"></a>カスタム DNS 名を Azure Web アプリにマップする

このチュートリアルでは、[Azure App Service](../app-service/app-service-value-prop-what-is.md)の Web アプリ、モバイル アプリ バックエンド、または API アプリにカスタム DNS 名 (バニティ ドメイン) をマップする方法について説明します。 

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

このチュートリアルでは、App Service のアプリに次の 2 つの DNS 名をマップするサンプル シナリオに従います。

- `contoso.com` - ルート ドメイン。 [A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)を使用して Azure にマップします。 
- `www.contoso.com` - `contoso.com` のサブドメイン。 [A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)または [CNAME レコード](https://en.wikipedia.org/wiki/CNAME_record)を使用します。

また、[ワイルドカード DNS](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (例: `*.contoso.com`) を App Service にマップする方法も示します。

## <a name="before-you-begin"></a>開始する前に

このチュートリアルでは DNS 名を Azure App Service にマップする方法を説明するため、各ドメイン プロバイダー (GoDaddy など) の DNS 構成ページへの管理アクセス権が必要です。 たとえば、`contoso.com` と `www.contoso.com` のマッピングを追加するには、`contoso.com` の DNS エントリを構成できる必要があります。

ドメイン プロバイダーにドメインをまだ登録していない場合は、[ドメインを Azure から直接購入してアプリにマップ](custom-dns-web-site-buydomains-web-app.md)できます。

## <a name="step-1---prepare-your-app"></a>手順 1 - アプリを準備する
カスタム DNS 名をマップするには、[App Service プラン](https://azure.microsoft.com/pricing/details/app-service/)が **Shared** レベル以上である必要があります。 この手順では、Azure アプリがサポートされている価格レベルであることを確認します。

### <a name="log-in-to-azure"></a>Azure へのログイン

Azure Portal を開きます。 

そのためには、Azure アカウントで [https://portal.azure.com](https://portal.azure.com) にサインインします。

### <a name="navigate-to-your-app"></a>アプリに移動する
左側のメニューで **[App Service]** をクリックし、Azure アプリの名前をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/select-app.png)

アプリの "_ブレード_" (水平方向に開かれるポータル ページ) が表示されます。  

### <a name="check-the-pricing-tier"></a>価格レベルを確認する
既定で表示される **[概要]** ページで、アプリが **Free** レベルではないことを確認します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

カスタム DNS は、**Free** レベルではサポートされていません。 スケールアップする必要がある場合は、次のセクションの手順に従います。 それ以外の場合は、[手順 2]() に進みます。

### <a name="scale-up-your-app-service-plan"></a>App Service プランをスケールアップする

プランをスケールアップするには、左側のウィンドウで **[スケール アップ (App Service のプラン)]** をクリックします。

スケールするレベルを選択します。 たとえば、**[Shared]** を選択します。 準備ができたら、**[選択]** をクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

次の通知が表示されたら、スケール操作は完了です。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/scale-notification.png)

<a name="info"></a>

## <a name="step-2---get-mapping-information"></a>手順 2 - マッピング情報を取得する

この手順では、後で DNS レコードを作成する際に必要となる情報を取得します。 

### <a name="open-the-custom-domain-ui"></a>カスタム ドメインの UI を開く

アプリのブレードで、メニューの **[カスタム ドメイン]** をクリックします。 

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

### <a name="copy-the-mapping-information"></a>マッピング情報をコピーする

**[カスタム ドメイン]** ページで、アプリの **IP アドレス**と、**[サイトに割り当てられるホスト名]** の既定のホスト名をコピーします。

IP アドレスは A レコードのマッピングに、既定のホスト名は CNAME レコードのマッピングにそれぞれ必要になります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/mapping-information.png)

## <a name="step-3---add-dns-records"></a>手順 3 - DNS レコードを追加する 

この手順では、カスタム DNS をアプリにマップするために必要な DNS レコードを追加します。 この手順は、独自のドメイン プロバイダーで実行します。

### <a name="access-dns-records-with-domain-provider"></a>ドメイン プロバイダーで DNS レコードにアクセスする

まず、ドメイン プロバイダーの Web サイトにログインします。

次に、DNS レコードの管理ページを見つける必要があります。 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、ご使用のプロバイダーのドキュメントを参照してください。 通常は、**[ドメイン名]**、**[DNS]**、または **[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。 通常、リンクを見つけるには、アカウント情報を表示し、 **[ドメイン]**などのリンクを探します。 

次に、DNS レコードを管理するためのリンクを探します。 このリンクには、**[ゾーン ファイル]**、**[DNS レコード]**、または **[詳細構成]** という名前が付けられている場合があります。

次のスクリーンショットは、DNS レコード ページの例を示しています。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/example-record-ui.png)

このスクリーンショットの例では、**[追加]** をクリックしてレコードを作成します。 プロバイダーによっては、追加するレコード タイプごとに異なるリンクが用意されています。 この場合も、プロバイダーのドキュメントを参照してください。

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、DNS レコードの作成後、別の **[変更を保存]** リンクをクリックするまで DNS レコードの変更は反映されません。 
>
>

<a name="a"></a>

### <a name="create-an-a-record"></a>A レコードを作成する

この例では、ルート ドメイン `contoso.com` の A レコードを追加します。 

A レコードをアプリにマップする場合、App Service では実際には次の _2 つ_の DNS レコードが必要になります。

- アプリの IP アドレスにマップするための **A** レコード。
- アプリの既定のホスト名にマップするための **TXT** レコード。 このレコードにより、App Service はマップするカスタム ドメインの所有者を確認できます。

次の表に、サポートされているドメインの種類の A レコードのマッピングを構成する方法を示します (通常、`@` はルート ドメインを表します)。 

<table cellspacing="0" border="1">
  <tr>
    <th>ドメインの種類</th>
    <th>レコード タイプ</th>
    <th>Host</th>
    <th>値</th>
  </tr>
  <tr>
    <td rowspan="2" align="left">ルート ドメイン<br>(例: <code>contoso.com</code>)</td>
    <td>A</td>
    <td>@</td>
    <td><a href="#info">手順 2</a> で取得した IP アドレス</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>@</td>
    <td><a href="#info">手順 2</a> で取得した既定のホスト名</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">サブドメイン<br>(例: <code>www.contoso.com</code>)</td>
    <td>A</td>
    <td>www</td>
    <td><a href="#info">手順 2</a> で取得した IP アドレス</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>www</td>
    <td><a href="#info">手順 2</a> で取得した既定のホスト名</td>
  </tr>
  <tr>
    <td rowspan="2" align="left">ワイルドカード ドメイン<br>(例: <code>*.contoso.com</code>)</td>
    <td>A</td>
    <td>\*</td>
    <td><a href="#info">手順 2</a> で取得した IP アドレス</td>
  </tr>
  <tr>
    <td>TXT</td>
    <td>\*</td>
    <td><a href="#info">手順 2</a> で取得した既定のホスト名</td>
  </tr>
</table>

`contoso.com` ドメインの場合、DNS レコード ページは次のスクリーンショットのようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/a-record.png)

<a name="cname"></a>

### <a name="create-a-cname-record"></a>CNAME レコードを作成する
この例では、ルート ドメイン `contoso.com` の CNAME レコードを追加します。 

[A レコードのマッピング](#a)とは異なり、CNAME レコードをアプリにマップするときには、追加のレコードは不要です。

> [!IMPORTANT]
> CNAME マッピングを使用することをお勧めします。 アプリを削除して再作成した場合や、専用ホスティング レベルから **Shared** レベルに変更した場合、アプリの仮想 IP アドレスが変更される可能性があります。 このような変更が行われても CNAME マッピングは有効ですが、A マッピングは新しい IP アドレスにより無効になる可能性があります。 
>
> ただし、ルート ドメインの CNAME レコード ("ルート レコード") は作成_しない_でください。 詳細については、 [CNAME レコードをルート ドメインで使用できない理由](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)に関するページを参照してください。
> ルート ドメインを Azure アプリにマップするには、CNAME レコードではなく [A レコード](#a) を使用します。
> 
> 

次の表に、サポートされているドメインの種類の CNAME レコードのマッピングを構成する方法を示します。

CNAME レコードを次のように構成します。

<table cellspacing="0" border="1">
  <tr>
    <th>ドメインの種類</th>
    <th>CNAME ホスト</th>
    <th>CNAME 値</th>
  </tr>
  <tr>
    <td>サブドメイン<br>(例: <code>www.contoso.com</code>)</td>
    <td>www</td>
    <td><a href="#info">手順 2</a> で取得した既定のホスト名</td>
  </tr>
  <tr>
    <td>ワイルドカード ドメイン<br>(例: <code>*.contoso.com</code>)</td>
    <td>\*</td>
    <td><a href="#info">手順 2</a> で取得した既定のホスト名</td>
  </tr>
</table>

`www.contoso.com` ドメインの場合、DNS レコード ページは次のスクリーンショットのようになります。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record.png)

<a name="enable"></a>

## <a name="step-4---enable-the-custom-dns-in-your-app"></a>手順 4 - アプリでカスタム DNS を有効にする

構成した DNS 名 (例: `contoso.com` と `www.contoso.com`) をアプリに追加する準備ができました。

Azure Portal のアプリの **[カスタム ドメイン]** ページに戻り ([手順 1](#info) を参照)、カスタム ドメインの完全修飾ドメイン名 (FQDN) を一覧に追加する必要があります。

### <a name="add-the-a-hostname-to-your-app"></a>アプリに A ホスト名を追加する

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/add-host-name.png)

先ほど A レコードを構成した FQDN (例: `contoso.com`) を入力し、**[検証]** をクリックします。

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/verification-error.png)

それ以外の場合は、**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

<a name="add-cname"></a>
### <a name="add-the-cname-hostname-to-your-app"></a>アプリに CNAME ホスト名を追加する

**[ホスト名の追加]** の横の **+** アイコンをクリックします。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

先ほど CNAME レコードを構成した FQDN (例: `www.contoso.com`) を入力し、**[検証]** をクリックします。

手順を飛ばしていたり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

それ以外の場合は、**[ホスト名の追加]** ボタンがアクティブになります。 

**[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。

**[ホスト名の追加]** をクリックして、アプリに DNS 名を追加します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

アプリの **[カスタム ドメイン]** ページに新しいホスト名が反映されるまで時間がかかることがあります。 データを更新するために、ブラウザーの表示を更新してみてください。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

### <a name="step-5---test-in-browser"></a>手順 5 - ブラウザーでテストする

ブラウザーで、構成済みの DNS 名 (`contoso.com` と `www.contoso.com`) を参照します。

![Azure アプリへのポータル ナビゲーション](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

## <a name="more-resources"></a>その他のリソース

[Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)

