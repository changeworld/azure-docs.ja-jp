<properties title="DomainDiscover (TierraNet) で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します" pageTitle="トラフィック マネージャーを利用する Azure の Web サイトの DomainDiscover ドメイン名の構成" metaKeywords="Windows Azure, Windows Azure Web Sites, DomainDiscover, TierraNet, トラフィック マネージャー" description="DomainDiscover (TierraNet) で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します。" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#トラフィック マネージャーを利用する Windows Azure の Web サイトのカスタム ドメイン名の構成 (DomainDiscover (TierraNet))

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト" class="current">トラフィック マネージャーを利用する Web サイト</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[DomainDiscover.com](https://domaindiscover.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。DomainDiscover.com は [TierraNet](https://www.tierra.net/) のサービスの一つです。

[WACOM.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [Web サイトの標準モード用の構成](#bkmk_configsharedmode)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web サイトに対するトラフィック マネージャーの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Web サイトの標準モード用の構成</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure の Web サイトに関連付けるには、DomainDiscover のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DomainDiscover.com の DNS ツールを見つけます。

1. **[Login]** メニューの **[Control Panel]** をクリックして、DomainDiscover.com (TierraNet) のアカウントにログオンします。

    ![DomainDiscover の [Login] メニュー](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_LoginMenu.png)

2. **[Domain Services]** ページで、Azure の Web サイトで使用するドメインを選択します。

    ![ドメイン管理ページ](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DomainManagement.png)

3. ドメイン設定で、**[DNS Service]** の **[Edit]** をクリックします。

    ![DNS の [Edit] ボタン](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSEditButton.png)

4. **[Manage DNS]** ウィンドウで、DNS レコードのタイプを選択して **[Add Records]** ボックスの一覧に追加します。その後、**[Add]** をクリックします。

    ![DNS の [Edit] ボタン](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSAddRecords.png)

5. 次のページで、DNS レコードの値を入力します。その後、**[Add]** をクリックします。

    ![DNS の [Edit] ボタン](.\media\web-sites-domaindiscover-custom-domain-name\DomainDiscover_DNSRecords_TM.png)

    * CNAME レコードを追加するときは、まず **[Manage DNS]** ページで **[CNAME (Alias)]** を選択する必要があります。その後、**[Host]** フィールドを、使用するサブドメインに設定します。たとえば **www** にします。**[Alias Hostname]** フィールドを、Azure の Web サイトで利用するトラフィック マネージャー プロファイルの **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。その後、Time-to-Live (TTL) の値を 1,800 (秒) などに設定します。

	    > [WACOM.NOTE] CNAME レコードを使用する必要があるのは、トラフィック マネージャーを利用して負荷分散されている Web サイトにカスタム ドメイン名を関連付けるときのみです。

<h2><a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

