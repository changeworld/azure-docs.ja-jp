<properties title="Register.com で登録したドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します" pageTitle="トラフィック マネージャーを利用する Azure の Web サイトの Register.com ドメイン名の構成" metaKeywords="Windows Azure, Windows Azure の Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

# トラフィック マネージャーを利用する Azure の Web サイトのカスタム ドメイン名の構成 (Register.com)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com" class="current">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-registerdotcom-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト" class="current">トラフィック マネージャーを利用する Web サイト</a></div>


[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、[Register.com](https://register.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、Register.com のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. Register.com のアカウントにログオンし、右上にある **[Your Account]** を選択してドメインを表示したら、カスタム ドメイン名を選択します

	![[My Account] ページ](./media/web-sites-custom-domain-name/rdotcom-myaccount.png)

3. **[Advanced Technical Settings]** ページが表示されるまでページを下にスクロールします。このセクションのリンクからドメインのレコードを管理できます。CNAME レコードの場合、**[Edit Domain Aliases Records]** リンクを使用します。

	![[Advanced Technical Settings] ページ](./media/web-sites-custom-domain-name/rdotcom-advancedsettingstm.png)

5. **[Edit]** をクリックすると、既存のレコードの変更や新しいレコードの追加に使用できるフォームが表示されます。このフォームは CNAME レコードと A レコードとで似ています。

	* CNAME レコードを追加するときは、**[.mydomainname.com]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[points to]** 値として、Azure の Web サイトで利用するトラフィック マネージャーの **.trafficmanager.net** ドメイン名を選択する必要があります。たとえば **contoso.trafficmanager.net** にします。**[Refers to Host Name]** は **[Select]** のままにします。Azure の Web サイトで使用する CNAME レコードを作成するとき、このフィールドは不要なためです。
	
		![CNAME フォーム](./media/web-sites-custom-domain-name/rdotcom-editcnamerecordtm.png)


5. レコードの追加または変更が完了したら、**[Continue]** をクリックして変更を確認します。**[Continue]** を再びクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

