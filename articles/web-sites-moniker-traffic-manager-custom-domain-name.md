<properties title="Moniker で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します" pageTitle="トラフィック マネージャーを利用する Azure の Web サイトの Moniker ドメイン名の構成" metaKeywords="Windows Azure, Windows Azure の Web サイト, Moniker, トラフィック マネージャー" description="Moniker で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します。" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#トラフィック マネージャーを利用する Azure の Web サイトのカスタム ドメイン名の構成 (Moniker)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker" class="current">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-moniker-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト" class="current">トラフィック マネージャーを利用する Web サイト</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[Moniker](https://moniker.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、Moniker のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Moniker.com の DNS ツールを見つけます。

1. Moniker.com のアカウントにログオンし、**[My Domains]**、**[Manage Templates]** の順に選択します。

    ![Moniker の [My Domains] ページ](.\media\web-sites-moniker-custom-domain-name\Moniker_MyDomains.png)

2. **[Zone Template Management]** ページで、**[Create New Template]** を選択します。

    ![Moniker の [Zone Template Management] ページ](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneManager.png)

3. **[Template Name]** に入力します。

5. **[Record Type]** を選択して DNS レコードを作成します。次に、**[Hostname]** と **[Address]** に入力します。

    ![Moniker の [Create Zone Template] ページ](.\media\web-sites-moniker-custom-domain-name\Moniker_CreateZoneTemplate_TM.png)

    * CNAME レコードを追加するときは、**[Hostname]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Address]** フィールドを、Azure の Web サイトで利用するトラフィック マネージャーの **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。

	    > [WACOM.NOTE] CNAME レコードを使用する必要があるのは、トラフィック マネージャーを利用して負荷分散されている Web サイトにカスタム ドメイン名を関連付けるときのみです。
	 
7. **[Add]** をクリックしてエントリを追加します。

8. すべてのエントリを追加した後、**[Save]** をクリックします。

5. [**Domain Manager**] を選択してドメインの一覧に戻ります。

6. ターゲット ドメインのチェック ボックスをオンにし、**[Manage Templates]** を再びクリックします。

7. 前の手順で作成した新しいテンプレートを見つけます。次に、**[place selected domains (1) into this Template]** リンクをクリックします。

    ![Moniker の [Create Zone Template] ページ](.\media\web-sites-moniker-custom-domain-name\Moniker_ZoneAssignment.png)

<h2><a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

