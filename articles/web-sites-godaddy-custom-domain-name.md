<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Configure a GoDaddy domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

# Azure Website のカスタム ドメイン名の構成 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Go Daddy][Go Daddy] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトでのドメインの有効化][Web サイトでのドメインの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

## <a name="bkmk_configurecname"></a>カスタム ドメインの DNS レコードの追加

カスタム ドメインを Azure Websites に関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して GoDaddy.com の DNS ツールを見つけます。

1.  GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage my domains]** の順に選択します。最後に、Azure Website で使用するドメイン名をドロップ ダウン メニューで選択し、**[Manage DNS]** を選択します。

    ![GoDaddy のカスタム ドメイン ページ][GoDaddy のカスタム ドメイン ページ]

2.  **[Domain details]** ページの **[DNS Zone File]** タブまでスクロールします。このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。

    ![DNS Zone File タブ][DNS Zone File タブ]

    既存のレコードを追加するには、**[Add Record]** を選択します。

    既存のレコードを**編集**するには、レコードの横にあるペンと紙のアイコンを選択します。

    > [WACOM.NOTE] 新しいレコードを追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されています。使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。

3.  レコードを追加する場合は、まずレコードの種類を選択する必要があります。

    ![レコードの種類の選択][レコードの種類の選択]

    次に、**[Host]** (カスタム ドメインまたはサブドメイン) および **[Points to]** を指定する必要があります。

    ![ゾーン レコードの追加][ゾーン レコードの追加]

    -   **A (ホスト) レコード**を追加するときは、**[Host]** フィールドを、<**@*>\* (**contoso.com** などのルート ドメイン名) か、\* (複数のサブドメインに一致するワイルドカード) か、使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Points to]** フィールドを、Azure Website の IP アドレスに設定する必要があります。

        > [WACOM.NOTE] A (ホスト) レコードを使用する場合は、次の構成を持つ CNAME レコードも追加する必要があります。
        >
        > -   **[Host]** 値 **awverify**、**[Points to]** 値 **awverify.\<yourwebsitename\>.azurewebsites.net**。
        >
        > この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

    -   **CNAME (エイリアス) レコード**を追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points to]** フィールドを、Azure Website の **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

4.  レコードの追加または変更が完了したら、**[Finish]** をクリックして変更を保存します。

## <a name="enabledomain"></a>Web サイトでのドメイン名の有効化

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

  [Go Daddy]: https://godaddy.com
  [DNS レコードについて]: #understanding-records
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトでのドメインの有効化]: #enabledomain
  [GoDaddy のカスタム ドメイン ページ]: ./media/web-sites-custom-domain-name/godaddy-customdomain.png
  [DNS Zone File タブ]: ./media/web-sites-custom-domain-name/godaddy-zonetab.png
  [レコードの種類の選択]: ./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png
  [ゾーン レコードの追加]: ./media/web-sites-custom-domain-name/godaddy-addzonerecord.png
