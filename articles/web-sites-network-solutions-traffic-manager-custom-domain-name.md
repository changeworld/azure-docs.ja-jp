<properties title="Network Solutions で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します" pageTitle="トラフィック マネージャーを利用する Azure の Web サイトの Network Solutions ドメイン名の構成" metaKeywords="Azure, Azure の Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#トラフィック マネージャーを利用する Azure の Web サイトのカスタム ドメイン名の構成 (Network Solutions)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions" class="current">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-network-solutions-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト" class="current">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[Network Solutions](https://networksolutions.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、Network Solutions のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. Network Solutions のアカウントにログオンし、右上にある **[My Account]** を選択します。

3. **[My Products and Services]** タブで **[Edit DNS]** を選択します。

	![[Edit DNS] ページ](./media/web-sites-custom-domain-name/ns-editdns.png)

2. **[Domain Names]** ページの **[Manage <yourdomainname>]** セクションで、**[Edit Advanced DNS Records]** を選択します。

	![[Domain Names] ページで [Edit Advanced DNS Records] を選択したところ](./media/web-sites-custom-domain-name/ns-editadvanced.png)

4. **[Update Advanced DNS]** ページには、レコード タイプごとのセクションがあり、各セクションの下には **[Edit]** ボタンがあります。CNAME レコードの場合、**[Host Alias (CNAME Records)]** セクションを使用します。

	![[Update Advanced DNS] ページ](./media/web-sites-custom-domain-name/ns-updatecnameadvanced.png)

5. **[Edit]** をクリックすると、既存のレコードの変更や新しいレコードの追加に使用できるフォームが表示されます。

	CNAME レコードを追加するときは、**[Alias]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Other host]** フィールドの横にあるラジオ ボタンをクリックし、**[Other host]** を、Azure の Web サイトで利用するトラフィック マネージャー プロファイルの **.trafficmanager.net** ドメイン名に設定する必要があります。たとえば **contoso.trafficmanager.net** にします。**[Refers to Host Name]** は **[Select]** のままにします。Azure の Web サイトで使用する CNAME レコードを作成するとき、このフィールドは不要なためです。
	
	![CNAME フォーム](./media/web-sites-custom-domain-name/ns-cnametm.png)

5. レコードの追加または変更が完了したら、**[Continue]** をクリックして変更を確認します。**[Save changes only]** を選択して変更を保存します。

<h2><a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

