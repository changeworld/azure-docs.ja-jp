<properties title="GoDaddy で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します" pageTitle="Azure の Web サイトの GoDaddy ドメイン名の構成" metaKeywords="Azure, Azure の Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Azure の Web サイトのカスタム ドメイン名の構成 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Go Daddy](https://godaddy.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

<h2><a name="bkmk_configurecname"></a>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure の Web サイトに関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して GoDaddy.com の DNS ツールを見つけます。

1. GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage your domains]** の順に選択します。最後に、Azure の Web サイトで使用するドメイン名を選択します。

	![GoDaddy のカスタム ドメイン ページ](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. **[Domain details]** ページの **[DNS Zone File]** タブを選択します。このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。**[Edit]** をクリックして **Zone File Editor** を表示します。

	![[DNS Zone File] タブ](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

4. **Zone File Editor** はレコード タイプごとのセクションに分かれています。セクションは A レコード (**[A (Host)]** として表示) から始まり、CNAME レコード (**[CNAME (Alias)]** として表示) が続きます。新しいエントリを追加するには、対応するセクションの下にある **[Quick Add]** を使用します。既存のエントリを編集するには、そのエントリを選択し、既存の情報を変更します。

	> [WACOM.NOTE] エントリをゾーン ファイルに追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されています。使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。

	* CNAME レコードを追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points to]** フィールドを、Azure の Web サイトの **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

		![Zone File Editor](./media/web-sites-custom-domain-name/godaddy-quickaddcname.png)
	
		> [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		> * **[Host]** 値が **www**、**[Points to]** 値が **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> または
		> 
		> * **[Host]** 値が **awverify.www**、**[Points to]** 値が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* A レコードを追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名) か、* (複数のサブドメインに一致するワイルドカード) か、使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Points to]** フィールドを、Azure の Web サイトの IP アドレスに設定する必要があります。

		![レコードの Zone File Editor ](./media/web-sites-custom-domain-name/godaddy-quickaddarecord.png)

5. レコードの追加または変更が完了したら、**[Save Zone File]** をクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

