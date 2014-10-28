<properties title="Learn how to configure an Azure website to use a domain name registered with Moniker" pageTitle="Configure a Moniker domain name for an Azure website" metaKeywords="Azure, Azure Web Sites, domain name" description="Learn how to configure an Azure website to use a domain name registered with Moniker" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Azure Website のカスタム ドメイン名の構成 (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

この記事では、[Moniker.com][Moniker.com] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

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
カスタム ドメインを Azure Websites に関連付けるには、Moniker のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Moniker.com の DNS ツールを見つけます。

1.  Moniker.com のアカウントにログオンし、**[My Domains]**、**[Manage Templates]** の順に選択します。

    ![Moniker の My Domains ページ][Moniker の My Domains ページ]

2.  **[Zone Template Management]** ページで、**[Create New Template]** を選択します。

    ![Moniker の Zone Template Management ページ][Moniker の Zone Template Management ページ]

3.  **[Template Name]** に入力します。

4.  **[Record Type]** を選択して DNS レコードを作成します。次に、**[Hostname]** と **[Address]** に入力します。

    ![Moniker の Create Zone Template ページ][Moniker の Create Zone Template ページ]

    -   CNAME レコードを追加するときは、**[Hostname]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Address]** フィールドを、Azure Websites の **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

    -   A レコードを追加するときは、**[Hostname]** フィールドを <**@*>\* (**contoso.com** などのルート ドメイン名) または使用するサブドメイン (**www** など) に設定する必要があります。**[Address]** フィールドを、Azure Websites の IP アドレスに設定する必要があります。

        > [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
        >
        > -   **[Hostname]** の値が **www**、**[Address]** の値が **\<yourwebsitename\>.azurewebsites.net**。
        >
        > または
        >
        > -   **[Hostname]** の値が **awverify.www**、**[Address]** の値が **awverify.\<yourwebsitename\>.azurewebsites.net**。
        >
        > この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

5.  **[Add]** をクリックしてエントリを追加します。

6.  すべてのエントリを追加した後、**[Save]** をクリックします。

7.  **[Domain Manager]** を選択してドメインの一覧に戻ります。

8.  ターゲット ドメインのチェック ボックスをオンにし、**[Manage Templates]** を再びクリックします。

9.  前の手順で作成した新しいテンプレートを見つけます。次に、**[place selected domains (1) into this Template]** リンクをクリックします。

    ![Moniker の Create Zone Template ページ][1]

## <a name="enabledomain"></a>Web サイトでのドメイン名の有効化

[WACOM.INCLUDE [modes][2]]

  [カスタム ドメイン]: /ja-jp/documentation/articles/web-sites-custom-domain-name "カスタム ドメイン"
  [GoDaddy]: /ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ja-jp/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ja-jp/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ja-jp/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ja-jp/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Web サイト]: /ja-jp/documentation/articles/web-sites-moniker-custom-domain-name/ "Web サイト"
  [Traffic Manager を利用する Web サイト]: /ja-jp/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/ "Traffic Manager を利用する Web サイト"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro.md
  [Moniker.com]: https://moniker.com
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS レコードについて]: #understanding-records
  [Web サイトの基本、共有、または標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトでのドメインの有効化]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-raw.md
  [modes]: ../includes/custom-dns-web-site-modes.md
  [Moniker の My Domains ページ]: .\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png
  [Moniker の Zone Template Management ページ]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png
  [Moniker の Create Zone Template ページ]: .\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate.png
  [1]: .\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png
  [2]: ../includes/custom-dns-web-site-enable-on-web-site.md
