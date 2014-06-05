<properties title="eNom で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します" pageTitle="Azure の Web サイトの eNom ドメイン名の構成" metaKeywords="Windows Azure, Windows Azure の Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Azure の Web サイトのカスタム ドメイン名の構成 (eNom)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom" class="current">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-enom-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[eNom](https://enom.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、eNom のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して eNom.com の DNS ツールを見つけます。

1. eNom のアカウントにログオンし、**[Domains]**、**[My Domains]** の順に選択します。これにより、ドメイン名が表示されます。

2. **[My Domains]** ページで、**[Manage Domain]** フィールドを使用して **[Host Records]** を選択します。これにより、ホスト レコードのフィールドが表示されます。

	![[DNS Zone File] タブ](./media/web-sites-custom-domain-name/e-hostrecords.png)

4. Host Records editor では、**[Record Type]** フィールドを使用して特定のレコード タイプを選択できます。Azure の Web サイトの場合、**[CNAME (Alias)]** または **[A (Address)]** のみを選択して使用する必要があります。

	![Zone File Editor](./media/web-sites-custom-domain-name/e-editrecords.png)

	> [WACOM.NOTE] エントリをゾーン ファイルに追加する前に、eNom ではルート ドメイン ('@') とワイルドカード サブドメイン (''\*') の DNS レコードが既に作成されています。ルート ドメインを Web サイトにリダイレクトするか、ワイルドカード サブドメインの A レコードを使用する場合は、新しいエントリを作成せずに、これらの既存のエントリを変更する必要があります。

	* CNAME レコードを追加するときは、**[Host Name]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Address]** フィールドを、Azure の Web サイトの **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

		> [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		> * **[Alias]** 値が **www**、**[Other host]** 値が **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> または
		> 
		> * **[Alias]** 値が **awverify.www**、**[Other host]** 値が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* A レコードを追加するときは、**[Host Name]** フィールドを、**@** (**contoso.com** などのルート ドメイン名) か、* (複数のサブドメインに一致するワイルドカード) か、使用する特定のサブドメイン (**www** など) のいずれかに設定する必要があります。**[Address]** フィールドを、Azure の Web サイトの IP アドレスに設定する必要があります。

		> [WACOM.NOTE] A レコードを追加するときは、ホストが **awverify**、**[Points to]** が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net の CNAME レコードも追加する必要があります。

5. レコードの追加または変更が完了したら、**[Save]** をクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

