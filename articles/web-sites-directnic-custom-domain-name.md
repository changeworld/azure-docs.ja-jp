<properties title="Directnic で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します" pageTitle="Azure の Web サイトの Directnic ドメイン名の構成" metaKeywords="Azure, Azure の Web サイト, Directnic" description="Directnic で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します。" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Azure の Web サイトのカスタム ドメイン名の構成 (Directnic)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic" class="current">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-directnic-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[DirectNic.com](https://directnic.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [Web サイトの基本、共有、または標準モード用の構成](#bkmk_configsharedmode)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web サイトでのドメインの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]

<h2><a name="bkmk_configsharedmode"></a>Web サイトの基本、共有、または標準モード用の構成</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure の Web サイトに関連付けるには、Directnic のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Directnic.com の DNS ツールを見つけます。

1. Directnic.com のアカウントにログオンし、**[My Services]**、**[Domains]** の順に選択します。

    ![Directnic の [Services] メニュー](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainMenu.png)

2. Azure の Web サイトで使用するドメイン名をクリックします。

2. ドメインの管理ページの **[Services]** パネルで、**[DNS]** の **[Manage]** をクリックします。

    ![[Services] パネル](.\media\web-sites-directnic-custom-domain-name\Directnic_DomainManagement.png)

4. **[Type]**、**[Name]**、**[Data]** の各フィールドに入力することで、DNS レコードを追加します。完了したら、**[Add Record]** をクリックします。

    ![](.\media\web-sites-directnic-custom-domain-name\Directnic_DNS.png)

    * CNAME レコードを追加するときは、**[Name]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Data]** フィールドを、Azure の Web サイトの **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

    * A レコードを追加するときは、**[Name]** フィールドを、**@** (**contoso.com** などのルート ドメイン名) か、使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Data]** フィールドを、Azure の Web サイトの IP アドレスに設定する必要があります。

		> [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		> * **[Name]** 値が **www**、**[Data]** 値が **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> または
		> 
		> * **[Name]** 値が **awverify.www**、**[Data]** 値が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。


<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

