<properties 
	pageTitle="Traffic Manager を利用する Azure Website の DomainDiscover ドメイン名の構成" 
	description="Traffic Manager を使用する Azure Web サイトを、DomainDiscover - TierraNet に登録したドメイン名を使用するように構成する方法について説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr,jroth"/>

#Traffic Manager を利用する Windows Azure Website のカスタム ドメイン名の構成 (DomainDiscover (TierraNet))

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">ネットワーク ソリューション</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover" class="current">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name/" title="Websites">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-domaindiscover-traffic-manager-custom-domain-name/" title="Website using Traffic Manager" class="current">Traffic Manager を使用する Web サイト</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[AZURE.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[DomainDiscover.com](https://domaindiscover.com) から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。DomainDiscover.com は [TierraNet](https://www.tierra.net/) のサービスの一つです。

[AZURE.INCLUDE [tmwebsitefooter](../includes/custom-dns-web-site-traffic-manager-notes.md)]

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [Web サイトの標準モード用の構成](#bkmk_configsharedmode)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web サイトに対するトラフィック マネージャーの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[AZURE.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-traffic-manager.md)]

<h2><a name="bkmk_configsharedmode"></a>Web サイトの標準モード用の構成</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes-traffic-manager.md)]

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure Websites に関連付けるには、DomainDiscover のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DomainDiscover.com の DNS ツールを見つけます。

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

    * CNAME レコードを追加するときは、まず **[Manage DNS]** ページで **[CNAME (Alias)]** を選択する必要があります。その後、**[Host]** フィールドを、使用するサブドメインに設定します。たとえば **www** にします。**[Alias Hostname]** フィールドを、Azure Website で利用する Traffic Manager プロファイルの **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。その後、Time-to-Live (TTL) の値を 1,800 (秒) などに設定します。

	    > [AZURE.NOTE] Traffic Manager を利用して負荷分散されている Web サイトにカスタム ドメイン名を関連付けるときは、CNAME レコードのみを使用する必要があります。

<h2><a name="enabledomain"></a>Traffic Manager Web サイトを有効にする</h2>

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]


<!--HONumber=42-->
