<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" pageTitle="Configure a Dotster domain name for an Azure website using Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, Dotster, Traffic Manager" description="Learn how to configure an Azure website that uses Traffic Manager to use a domain name registered with Dotster" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr,jroth" />

# Traffic Manager を利用する Windows Azure Website のカスタム ドメイン名の構成 (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト" class="current">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[Dotster.com][Dotster.com] から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [Web サイトの標準モード用の構成][Web サイトの標準モード用の構成]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトに対するトラフィック マネージャーの有効化][Web サイトに対するトラフィック マネージャーの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

## <a name="bkmk_configsharedmode"></a>Web サイトの標準モード用の構成

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a>

## カスタム ドメインの DNS レコードの追加

</p>
カスタム ドメインを Azure Websites に関連付けるには、Dotster のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Dotster.com の DNS ツールを見つけます。

1.  Dotster.com のアカウントにログオンします。**[Domain]** メニューで、**[DomainCentral]** を選択します。

    ![Dotster の DomainCentral メニュー項目][Dotster の DomainCentral メニュー項目]

2.  ドメインを選択して設定の一覧を表示します。次に、**[Nameservers]** リンクをクリックします。

    ![Dotster のドメイン構成オプション][Dotster のドメイン構成オプション]

3.  **[Use different name servers]** を選択します。Dotster の DNS サービスを利用するには、ネーム サーバーとして ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com、ns4.nameresolve.com を指定する必要があります。

    ![Dotster のドメイン構成オプション][1]

    > [WACOM.NOTE] ネーム サーバーで変更が有効になるまで 24 ～ 48 時間かかることがありますそれまで、この記事の残りの手順を実行することはできません。

4.  DomainCentral で、ドメインを選択し、**[DNS]** を選択します。**[Modify]** ボックスの一覧で、DNS レコードのタイプとして **[CNAME Alias]** または **[A Record]** を選択して追加します。

    ![Dotster のドメイン構成オプション][2]

5.  次に、レコードの **[Host]** と **[Points To]** の各フィールドを指定します。完了したら、**[Add]** をクリックします。

    ![Dotster のドメイン構成オプション][3]

    -   CNAME レコードを追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points To]** フィールドを、Azure Website で利用する Traffic Manager の **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。

        > [WACOM.NOTE] CNAME レコードを使用する必要があるのは、トラフィック マネージャーを利用して負荷分散されている Web サイトにカスタム ドメイン名を関連付けるときのみです。

## <a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

  [Dotster.com]: https://dotster.com
  [DNS レコードについて]: #understanding-records
  [Web サイトの標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトに対するトラフィック マネージャーの有効化]: #enabledomain
  [Dotster の DomainCentral メニュー項目]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png
  [Dotster のドメイン構成オプション]: .\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png
  [1]: .\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png
  [2]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png
  [3]: .\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME_TM.png
