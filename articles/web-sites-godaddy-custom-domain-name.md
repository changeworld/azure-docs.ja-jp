<properties title="Learn how to configure an Azure website to use a domain name registered with GoDaddy" pageTitle="Azure Web サイトの GoDaddy ドメイン名の構成" metaKeywords="Azure, Azure Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth" />

#Azure Website のカスタム ドメイン名の構成 (GoDaddy)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="Custom Domain">Custom Domain</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy" class="current">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name/" title="Websites" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-godaddy-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Traffic Manager を使用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Go Daddy](https://godaddy.com) から購入したカスタム ドメイン名を Azure Websites で使用する手順を示します。

[WACOM.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:

-   [DNS レコードについて](#understanding-records)
-   [カスタム ドメインの DNS レコードの追加](#bkmk_configurecname)
-   [Web サイトでのドメインの有効化](#enabledomain)

<h2><a name="understanding-records"></a>DNS レコードについて</h2>

[WACOM.INCLUDE [understandingdns](../includes/custom-dns-web-site-understanding-dns-raw.md)]


<h2><a name="bkmk_configurecname"></a>カスタム ドメインの DNS レコードの追加</h2>

カスタム ドメインを Azure Websites に関連付けるには、GoDaddy のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順に従って GoDaddy.com の DNS ツールを見つけます。

1. GoDaddy.com のアカウントにログオンし、**[My Account]**、**[Manage my domains]** の順に選択します。最後に、Azure Website で使用するドメイン名をドロップ ダウン メニューで選択し、**[Manage DNS]** を選択します。

	![custom domain page for GoDaddy](./media/web-sites-custom-domain-name/godaddy-customdomain.png)

2. **[Domain details]** ページの **[DNS Zone File]** タブまでスクロールします。このセクションを使用して、ドメイン名の DNS レコードを追加したり変更したりします。 

	![DNS Zone File tab](./media/web-sites-custom-domain-name/godaddy-zonetab.png)

既存のレコードを追加するには、**[Add Record]** を選択します。

既存のレコードを**編集**するには、レコードの横にあるペンと紙のアイコンを選択します。

	> [WACOM.NOTE] 新しいレコードを追加する前に、GoDaddy では一般的なサブドメイン (Editor では **[Host]**) の DNS レコード (**[email]**、**[files]**、**[mail]** など) が既に作成されています。使用する名前が既に存在する場合は、新しいレコードを作成せずに既存のレコードを変更してください。

4. レコードを追加する場合は、まずレコードの種類を選択する必要があります。

	![select record type](./media/web-sites-custom-domain-name/godaddy-selectrecordtype.png)

次に、**[Host]** (カスタム ドメインまたはサブドメイン) および **[Points to]** を指定する必要があります。

	![add zone record](./media/web-sites-custom-domain-name/godaddy-addzonerecord.png)

	* **A (ホスト) レコード**を追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名) か、* (複数のサブドメインに一致するワイルドカード) か、使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Points To]** フィールドを、Azure の Web サイトの IP アドレスに設定する必要があります。
	
		> [WACOM.NOTE] A (ホスト) レコードを使用する場合は、次の構成を持つ CNAME レコードも追加する必要があります。
		> 
		> * **[Host]** 値 **awverify**、**[Points To]** 値 **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* **CNAME (エイリアス) レコード**を追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points to]** フィールドを、Azure Website の **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば、**contoso.azurwebsites.net** にします。


5. レコードの追加または変更が完了したら、**[Finish]** をクリックして変更を保存します。

<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

