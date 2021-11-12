---
title: チュートリアル:既存のカスタム DNS 名をマップする
description: 既存のカスタム DNS ドメイン名 (カスタム ドメイン) を、Azure App Service の Web アプリ、モバイル アプリ バックエンド、または API アプリに追加する方法について説明します。
keywords: App Service, Azure App Service, ドメイン マッピング, ドメイン名, 既存のドメイン, ホスト名, バニティ ドメイン
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.topic: tutorial
ms.date: 05/27/2021
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 607a1e8e12fa3840235e3158e98fb7dfb34a73fe
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/03/2021
ms.locfileid: "131447818"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>チュートリアル:既存のカスタム DNS 名を Azure App Service にマップする

[Azure App Service](overview.md) は、非常にスケーラブルな、自己適用型の Web ホスティング サービスを提供します。 このチュートリアルでは、既存のカスタム ドメイン ネーム システム (DNS) 名を App Service にマップする方法について説明します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * [CNAME レコード](https://en.wikipedia.org/wiki/CNAME_record)を使用してサブドメインをマップする。
> * [A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)を使用してルート ドメインをマップする。
> * CNAME レコードを使用して[ワイルドカード ドメイン](https://en.wikipedia.org/wiki/Wildcard_DNS_record)をマップする。
> * 既定の URL をカスタム ディレクトリにリダイレクトする。


## <a name="1-prepare-your-environment"></a>1.環境を準備する

* [App Service アプリを作成する](./index.yml)か、別のチュートリアルで作成したアプリを使用します。
* 自分のカスタム ドメインの DNS レコードを編集できることを確認します。 カスタム ドメインをまだお持ちでない場合は、[App Service ドメイン](manage-custom-dns-buy-domain.md)を購入できます。

    > [!NOTE]
    > DNS レコードを編集するには、自分のドメイン プロバイダー (GoDaddy など) の DNS レジストリへのアクセス権が必要です。 たとえば、`contoso.com` と `www.contoso.com` の DNS エントリを追加するには、`contoso.com` ルート ドメインに対して DNS 設定を構成できる必要があります。


## <a name="2-prepare-the-app"></a>2. アプリの準備

Web アプリにカスタム DNS 名をマップするには、Web アプリの [App Service プラン](overview-hosting-plans.md)が有料レベルである (**Free (F1)** ではない) 必要があります。

#### <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) を開き、Azure アカウントでサインインします。

#### <a name="select-the-app-in-the-azure-portal"></a>Azure portal でアプリを選択します

1. **[App Services]** を検索して選択します。

   ![App Services の選択を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. **[App Services]** ページで、Azure アプリの名前を選択します。

   ![Azure アプリへのポータル ナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service アプリの管理ページが表示されます。

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>価格レベルの確認

1. アプリ ページの左側のウィンドウで、 **[設定]** セクションまでスクロールし、 **[スケール アップ (App Service プラン)]** を選択します。

   ![[スケール アップ (App Service プラン)] メニューを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. アプリの現在のレベルが青色の枠線で強調表示されます。 アプリが **F1** レベルに含まれていないことを確認します。 カスタム DNS は、**F1** レベルではサポートされていません。

   ![[推奨される価格レベル] を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. App Service プランが **F1** レベルでない場合は、 **[スケール アップ]** ページを閉じて、「[3. ドメイン検証 ID を取得する](#3-get-a-domain-verification-id)」に進みます。

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>App Service プランをスケール アップする

1. 非 Free レベルのいずれかを選びます (**D1**、**B1**、**B2**、**B3**、または **運用** カテゴリのいずれかのレベル)。 その他のオプションについては、 **[その他のオプションを参照する]** をクリックします。

1. **[適用]** を選択します。

   ![価格レベルの確認を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   次の通知が表示されたら、スケール操作は完了です。

   ![スケール操作の確認を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/scale-notification.png)


## <a name="3-get-a-domain-verification-id"></a>3.ドメイン検証 ID を取得する

アプリにカスタム ドメインを追加するには、ドメイン プロバイダーで検証 ID を TXT レコードとして追加して、ドメインの所有権を確認する必要があります。 

1. アプリ ページの左側のウィンドウで、 **[カスタム ドメイン]** を選択します。 
1. 次の手順のために、 **[カスタム ドメイン]** ページの **[カスタム ドメイン検証 ID]** をコピーしておきます。

    ![[カスタム ドメイン検証 ID] ボックスの ID を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    > [!WARNING]
    > ドメイン検証 ID をカスタム ドメインに追加すると、未解決の DNS エントリが回避され、サブドメインの乗っ取りを防ぐのに役立ちます。 前にこの検証 ID を使わずに構成したカスタム ドメインについては、DNS レコードに検証 ID を追加して、同じリスクからそれらを保護する必要があります。 この一般的な重大度の高い脅威の詳細については、[サブドメインの乗っ取り](../security/fundamentals/subdomain-takeover.md)に関するページを参照してください。
    
<a name="info"></a>

3. **(A レコードのみ)** : [A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)をマップするには、アプリの外部 IP アドレスが必要です。 **[カスタム ドメイン]** ページで、 **[IP アドレス]** の値をコピーします。

   ![Azure アプリへの portal ナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/mapping-information.png)


## <a name="4-create-the-dns-records"></a>4.DNS レコードを作成する

1. ドメイン プロバイダーの Web サイトにサインインします。

    > [!NOTE]
    > 必要に応じて、Azure DNS を使用して自社のドメインの DNS レコードを管理したり、Azure App Service のカスタム DNS 名を構成したりできます。 詳細については、「[チュートリアル:Azure DNS でドメインをホストする](../dns/dns-delegate-domain-azure-dns.md)」を参照してください。

1. DNS レコードの管理ページを探します。 

    > [!NOTE]
    > 各ドメイン プロバイダーには独自の DNS レコード インターフェイスがあるので、プロバイダーのドキュメントを参照してください。 **[ドメイン名]** 、 **[DNS]** 、 **[ネーム サーバー管理]** というラベルが付いたサイトの領域を探します。
    >
    > 多くの場合、DNS レコードのページを見つけるには、アカウント情報を表示し、 **[マイドメイン]** などのリンクを探します。 そのページに移動し、 **[ゾーン ファイル]** 、 **[DNS レコード]** 、 **[詳細構成]** のような名前のリンクを探します。

   以下のスクリーンショットは、DNS レコード ページの例です。

   ![DNS レコード ページの例を示すスクリーンショット。](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. **[追加]** または適切なウィジェットを選択して、レコードを作成します。 

1. 作成するレコードの種類を選択して、手順に従います。 [CNAME レコード](https://en.wikipedia.org/wiki/CNAME_record)または [A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)のいずれかを使用して、カスタム DNS 名を App Service にマップします。

    > [!NOTE]
    > **選択するレコード**
    > 
    > * ルート ドメイン (`contoso.com` など) をマップするには、[A レコード](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)を使用します。 CNAME レコードはルート レコードには使用しないでください (詳しくは、[RFC 1912 セクション 2.4](https://datatracker.ietf.org/doc/html/rfc1912#section-2.4) を参照してください)。
    > * サブドメイン (`www.contoso.com` など) をマップするには、[CNAME レコード](https://en.wikipedia.org/wiki/CNAME_record)を使用します。
    > * A レコードを使用してサブドメインをアプリの IP アドレスに直接マップできますが、[IP アドレスは変更される](overview-inbound-outbound-ips.md#when-inbound-ip-changes)可能性があります。 代わりに、CNAME は変更の影響を受けにくいアプリの既定ホスト名にマップされます。
    > * [ワイルドカード ドメイン](https://en.wikipedia.org/wiki/Wildcard_DNS_record) (`*.contoso.com` など) をマップするには、CNAME レコードを使用します。
    
# <a name="cname"></a>[CNAME](#tab/cname)

`www.contoso.com` の `www` のようなサブドメインについては、次の表に従って 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `<subdomain>` (例: `www`) | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid.<subdomain>` (例: `asuid.www`) | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

![Azure アプリへのポータルのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

- `contoso.com` のようなルート ドメインについては、次の表に従って 2 つのレコードを作成します。

    | レコード タイプ | Host | 値 | 説明 |
    | - | - | - |
    | A | `@` | 「[アプリの IP アドレスをコピーする](#3-get-a-domain-verification-id)」で取得した IP アドレス | ドメイン マッピング自体 (通常、`@` はルート ドメインを表します)。 |
    | TXT | `asuid` | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid.<subdomain>` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 ルート ドメインの場合は、`asuid` を使用します。 |
    
    ![DNS レコード ページを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/a-record.png)
    
- 推奨される CNAME レコードの代わりに A レコードを使用してサブドメイン (`www.contoso.com` など) をマップするには、A レコードと TXT レコードが次の表のようになっている必要があります。

    |レコード タイプ|Host|値|
    |--- |--- |--- |
    |A|\<subdomain\> (例: www)|「アプリの IP アドレスをコピーする」で取得した IP アドレス|
    |TXT|asuid.\<subdomain\> (例: asuid.www)|前に取得した検証 ID|
    
# <a name="wildcard-cname"></a>[ワイルドカード (CNAME)](#tab/wildcard)

`*.contoso.com` の `*` のようなワイルドカード名については、次の表に従って 2 つのレコードを作成します。

| レコード タイプ | Host | 値 | 説明 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | ドメイン マッピング自体。 |
| TXT | `asuid` | [前に取得した検証 ID](#3-get-a-domain-verification-id) | App Service は、`asuid` TXT レコードにアクセスして、カスタム ドメインの所有権を確認します。 |

![Azure アプリへのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

> [!NOTE]
> 一部のプロバイダー (GoDaddy など) では、別の **[変更を保存]** リンクを選択するまで DNS レコードの変更が反映されません。

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

<a name="wildcard" aria-hidden="true"></a>

<a name="cname" aria-hidden="true"></a>

## <a name="5-enable-the-mapping-in-your-app"></a>5.アプリでマッピングを有効にする

1. Azure Portal のアプリ ページの左側に表示されるウィンドウで、 **[カスタム ドメイン]** を選択します。

    ![[カスタム ドメイン] メニューを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **[カスタム ドメインの追加]** を選択します。

    ![[ホスト名の追加] 項目を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. 先ほど CNAME レコードを追加した完全修飾ドメイン名 (`www.contoso.com` など) を入力します。

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが表示されます。

1. **[ホスト名レコード タイプ]** が **[CNAME (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![[カスタム ドメインの追加] ボタンを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    ![CNAME レコードの追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。

    手順を行っていなかったり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。

    ![検証エラーを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

# <a name="a"></a>[A](#tab/a)

3. 先ほど A レコードを構成した完全修飾ドメイン名 (`contoso.com` など) を入力します。 

1. **[検証]** を選択します。 **[カスタム ドメインの追加]** ページが開きます。

1. **[ホスト名レコード タイプ]** が **[A レコード (example.com)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    ![A レコードの追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。
    
    手順を行っていなかったり、どこかで入力ミスがあったりした場合、ページの下部に検証エラーが表示されます。
    
    ![検証エラーを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
# <a name="wildcard-cname"></a>[ワイルドカード (CNAME)](#tab/wildcard)

3. ワイルドカード ドメインと一致する完全修飾ドメイン名を入力します。 たとえば、`*.contoso.com` という例の場合には、`sub1.contoso.com`、`sub2.contoso.com`、`*.contoso.com`、またはワイルドカードのパターンに一致するその他の文字列を使用できます。 次に、 **[検証]** を選択します。

    **[カスタム ドメインの追加]** ボタンがアクティブになります。

1. **[ホスト名レコード タイプ]** が **[CNAME レコード (www\.example.com または任意のサブドメイン)]** に設定されていることを確認します。 **[カスタム ドメインの追加]** を選択します。

    ![アプリへの DNS 名の追加を示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    アプリの **[カスタム ドメイン]** ページに新しいカスタム ドメインが反映されるまで時間がかかることがあります。 ブラウザーを最新の情報に更新して、データを更新してみてください。

    > [!NOTE]
    > カスタム ドメインの警告ラベルは、まだ TLS/SSL 証明書にバインドされていないことを意味します。 ブラウザーからカスタム ドメインへの HTTPS 要求を実行すると、ブラウザーに応じてエラーまたは警告が表示されます。 TLS バインドを追加するには、「[Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)」を参照してください。

-----


## <a name="6-test-in-a-browser"></a>6.ブラウザーでテストする

先ほど構成した DNS 名を参照します。

![Azure アプリへのナビゲーションを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>

カスタム ドメインの URL を参照したときに HTTP 404 (見つかりません) エラーが発生した場合、最も一般的な原因は次の 2 つになります。

* 構成されているカスタム ドメインに A レコードまたは CNAME レコードがない。 アプリでマッピングを有効にした後に DNS レコードを削除している可能性がある。 <a href="https://www.nslookup.io/">オンライン DNS 参照</a>ツールを使用して DNS レコードが正しく構成されていることを確認します。
* クライアントのブラウザーが、ドメインの古い IP アドレスをキャッシュしている。 キャッシュをクリアして、DNS 解決をもう一度テストします。 Windows コンピューターでは、`ipconfig /flushdns` でキャッシュをクリアします。


## <a name="migrate-an-active-domain"></a>アクティブなドメインの移行

ライブ サイトとその DNS ドメイン名を App Service にダウンタイムなしで移行する方法については、「[Azure App Service へのアクティブな DNS 名の移行](manage-custom-dns-migrate-domain.md)」をご覧ください。

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>カスタム ディレクトリにリダイレクトする

> [!NOTE]
> 既定では、App Service は Web 要求をアプリ コードのルート ディレクトリに送信します。 しかし、特定の Web フレームワークはルート ディレクトリで開始されません。 たとえば、[Laravel](https://laravel.com/) は `public` サブディレクトリで開始されます。 `contoso.com` の DNS の例を続行する場合、そのようなアプリには `http://contoso.com/public` でアクセスできますが、通常は、代わりに `http://contoso.com` を `public` ディレクトリに送信します。

これは一般的なシナリオですが、実際はカスタム DNS マッピングに関わるものではなく、アプリ内の仮想ディレクトリのカスタマイズに関するものです。

1. Web アプリ ページの左側のペインで、 **[アプリケーション設定]** を選択します。

1. ページの下部でルート仮想ディレクトリ `/` が既定で `site\wwwroot` をポイントしていますが、これがお客様のアプリ コードのルート ディレクトリです。 たとえば、代わりに `site\wwwroot\public` をポイントするように変更して、変更内容を保存できます。

    ![仮想ディレクトリのカスタマイズを示すスクリーンショット。](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. 操作の完了後、ブラウザーで自分のアプリのルート パス (例: `http://contoso.com` や `http://<app-name>.azurewebsites.net`) に移動して確認を行います。


## <a name="automate-with-scripts"></a>スクリプトで自動化する

[Azure CLI](/cli/azure/install-azure-cli) または [Azure PowerShell](/powershell/azure/) を使用すると、カスタム ドメインの管理をスクリプトで自動化できます。

#### <a name="azure-cli"></a>Azure CLI

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。

```azurecli 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

詳細については、「[カスタム ドメインを Web アプリにマップする](scripts/cli-configure-custom-domain.md)」を参照してください

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

次のコマンドでは、構成済みカスタム DNS 名を App Service アプリに追加します。

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

詳細については、「[カスタム ドメインを Web アプリに割り当てる](scripts/powershell-configure-custom-domain.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

次のチュートリアルに進み、カスタム TLS/SSL 証明書を Web アプリにバインドする方法を学習してください。

> [!div class="nextstepaction"]
> [Azure App Service で TLS/SSL バインドを使用してカスタム DNS 名をセキュリティで保護する](configure-ssl-bindings.md)
