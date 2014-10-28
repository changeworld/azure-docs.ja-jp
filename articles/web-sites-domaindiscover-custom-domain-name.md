<properties title="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" pageTitle="Configure a DomainDiscover domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, DomainDiscover, TierraNet" description="Learn how to configure an Azure website to use a domain name registered with DomainDiscover - TierraNet" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Azure Website のカスタム ドメイン名の構成 (DomainDiscover (TierraNet))

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

この記事では、[DomainDiscover.com][DomainDiscover.com] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。DomainDiscover.com は TierraNet のサービスの一つです。

[WACOM.INCLUDE [introfooter][introfooter]]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [Web サイトの基本、共有、または標準モード用の構成][Web サイトの基本、共有、または標準モード用の構成]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトでのドメインの有効化][Web サイトでのドメインの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Web サイトの基本、共有、または標準モード用の構成

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## カスタム ドメインの DNS レコードの追加

</p>
カスタム ドメインを Azure Websites に関連付けるには、DomainDiscover のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DomainDiscover.com の DNS ツールを見つけます。

1.  **[Login]** メニューの **[Control Panel]** をクリックして、DomainDiscover.com (TierraNet) のアカウントにログオンします。

    ![DomainDiscover の Login メニュー][DomainDiscover の Login メニュー]

2.  **[Domain Services]** ページで、Azure の Web サイトで使用するドメインを選択します。

    ![ドメイン管理ページ][ドメイン管理ページ]

3.  ドメイン設定で、**[DNS Service]** の **[Edit]** ボタンをクリックします。

    ![DNS の Edit ボタン][DNS の Edit ボタン]

4.  **[Manage DNS]** ウィンドウの **[Add Records]** ボックスの一覧で、追加する DNS レコードのタイプを選択します。その後、**[Add]** をクリックします。

    ![DNS の Edit ボタン][1]

5.  次のページで、DNS レコードの値を入力します。その後、**[Add]** をクリックします。

    ![DNS の Edit ボタン][2]

    -   CNAME レコードを追加するときは、まず **[Manage DNS]** ページで **[CNAME (Alias)]** を選択する必要があります。その後、**[Host]** フィールドを、使用するサブドメインに設定します。たとえば **www** にします。**[Alias Hostname]** フィールドを、Azure Website の **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。その後、Time-to-Live (TTL) の値を 1,800 (秒) などに設定します。

    -   A レコードを追加する場合は、まず **[Manage DNS]** ページで **[A]** を選択する必要があります。その後、**[Host]** フィールドを、<**@*> (**contoso.com** などのルート ドメイン名) か、使用するサブドメイン (**www** など) のいずれかに設定します。**[IP Address]** フィールドを、Azure Website の IP アドレスに設定する必要があります。その後、Time-to-Live (TTL) の値を 1,800 (秒) などに設定します。

        > [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
        >
        > -   **[Name]** 値 **www**、**[Alias Hostname]** 値 **\<yourwebsitename\>.azurewebsites.net**。
        >
        > または
        >
        > -   **[Name]** 値 **awverify.www**、**[Alias Hostname]** 値 **awverify.\<yourwebsitename\>.azurewebsites.net**。
        >
        > この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

6.  A レコードを追加した場合、ドメインの既存の A レコードが非アクティブになっていないという警告が表示されることがあります。DomainDirect では、最近変更されたレコードでルート ドメイン名に既定の A レコードが既に設定されています。この設定をそのまま使用するか、**[DELETE]** をクリックして既定の A レコードを削除できます。

## <a name="enabledomain"></a>Web サイトでのドメイン名の有効化

[WACOM.INCLUDE [modes][3]]

  [カスタム ドメイン]: /ja-jp/documentation/articles/web-sites-custom-domain-name "カスタム ドメイン"
  [GoDaddy]: /ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ja-jp/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ja-jp/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ja-jp/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ja-jp/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Web サイト]: /ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name/ "Web サイト"
  [Traffic Manager を利用する Web サイト]: /ja-jp/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/ "Traffic Manager を利用する Web サイト"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [DomainDiscover.com]: https://domaindiscover.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS レコードについて]: #understanding-records
  [Web サイトの基本、共有、または標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトでのドメインの有効化]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [DomainDiscover の Login メニュー]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png
  [ドメイン管理ページ]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png
  [DNS の Edit ボタン]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png
  [1]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png
  [2]: .\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords.png
  [3]: ../includes/custom-dns-web-site-enable-on-web-site.md
