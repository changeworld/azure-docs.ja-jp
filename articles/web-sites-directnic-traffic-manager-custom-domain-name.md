<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" pageTitle="Configure a Directnic domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Directnic, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Directnic" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth"></tags>

# Traffic Manager を利用する Windows Azure Website のカスタム ドメイン名の構成 (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト" class="current">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

この記事では、[DirectNic.com][DirectNic.com] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [Web サイトの標準モード用の構成][Web サイトの標準モード用の構成]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトに対するトラフィック マネージャーの有効化][Web サイトに対するトラフィック マネージャーの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Web サイトの標準モード用の構成

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## カスタム ドメインの DNS レコードの追加

</p>
カスタム ドメインを Azure Websites に関連付けるには、Directnic のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Directnic.com の DNS ツールを見つけます。

1.  Directnic.com のアカウントにログオンし、**[My Services]**、**[Domains]** の順に選択します。

    ![Directnic の Services メニュー][Directnic の Services メニュー]

2.  Azure Websites で使用するドメイン名をクリックします。

3.  ドメインの管理ページの **[Services]** ウィンドウで、**[DNS]** の **[Manage]** ボタンをクリックします。

    ![Services パネル][Services パネル]

4.  **[Type]**、**[Name]**、**[Data]** の各フィールドに入力することで、DNS レコードを追加します。完了したら、**[Add Record]** をクリックします。

    ![][]

    -   CNAME レコードを追加するときは、**[Name]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Data]** フィールドを、Azure Website で利用する Traffic Manager プロファイルの **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。

        > [WACOM.NOTE] CNAME レコードを使用する必要があるのは、トラフィック マネージャーを利用して負荷分散されている Web サイトにカスタム ドメイン名を関連付けるときのみです。

## <a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化

[WACOM.INCLUDE [modes][1]]

  [カスタム ドメイン]: /ja-jp/documentation/articles/web-sites-custom-domain-name "カスタム ドメイン"
  [GoDaddy]: /ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ja-jp/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ja-jp/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ja-jp/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ja-jp/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Web サイト]: /ja-jp/documentation/articles/web-sites-directnic-custom-domain-name/ "Web サイト"
  [Traffic Manager を利用する Web サイト]: /ja-jp/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/ "Traffic Manager を利用する Web サイト"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [DirectNic.com]: https://directnic.com
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS レコードについて]: #understanding-records
  [Web サイトの標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトに対するトラフィック マネージャーの有効化]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [Directnic の Services メニュー]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png
  [Services パネル]: .\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png
  []: .\media\web-sites-directnic-custom-domain-name\Directnic_DNS_TM.png
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
