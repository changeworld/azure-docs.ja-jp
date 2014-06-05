<properties title="Dotster で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します" pageTitle="Azure の Web サイトの Dotster ドメイン名の構成" metaKeywords="Azure, Azure の Web サイト, Dotster" description="Dotster で登録したドメイン名を使用するように Azure の Web サイトを構成する方法について説明します。" services="web-sites" documentationCenter="" authors="larryfr,jroth" />

#Azure の Web サイトのカスタム ドメイン名の構成 (Dotster)

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster" class="current">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name/" title="Web サイト" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-dotster-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、[Dotster.com](https://dotster.com) から購入したカスタム ドメイン名を Azure の Web サイトで使用する手順を示します。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、Dotster のツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して Dotster.com の DNS ツールを見つけます。

1. Dotster.com のアカウントにログオンします。**[Domain]** メニューの **[DomainCentral]** をクリックします。

    ![Dotster の [DomainCentral] メニュー項目](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainCentralMenu.png)

2. ドメインを選択して設定の一覧を表示します。次に、**[Nameservers]** リンクをクリックします。

    ![Dotster のドメイン構成オプション](.\media\web-sites-dotster-custom-domain-name\Dotster_DomainMenu.png)

3. **[Use different name servers]** を選択します。Dotster の DNS サービスを利用するには、ネーム サーバーとして ns1.nameresolve.com、ns2.nameresolve.com、ns3.nameresolve.com、ns4.nameresolve.com を指定する必要があります。

    ![Dotster のドメイン構成オプション](.\media\web-sites-dotster-custom-domain-name\Dotster_Nameservers.png)

    > [WACOM.NOTE] ネーム サーバーで変更が有効になるまで 24 〜 48 時間かかることがあります。それまで、この記事の残りの手順を実行することはできません。

4. DomainCentral で、ドメインを選択し、**[DNS]** を選択します。**[Modify]** ボックスの一覧で、DNS レコードのタイプとして **[CNAME Alias]** または **[A Record]** を選択して追加します。

    ![Dotster のドメイン構成オプション](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS.png)

5. 次に、レコードの **[Host]** と **[Points To]** の各フィールドを指定します。完了したら、**[Add]** をクリックします。

    ![Dotster のドメイン構成オプション](.\media\web-sites-dotster-custom-domain-name\Dotster_DNS_CNAME.png)
 
    * CNAME レコードを追加するときは、**[Host]** フィールドを、使用するサブドメインに設定する必要があります。たとえば **www** にします。**[Points To]** フィールドを、Azure の Web サイトの **.azurewebsites.net** ドメイン名に設定する必要があります。たとえば **contoso.azurwebsites.net** にします。

    * A レコードを追加するときは、**[Host]** フィールドを、**@** (**contoso.com** などのルート ドメイン名) か、使用するサブドメイン (**www** など) のいずれかに設定する必要があります。**[Points To]** フィールドを、Azure の Web サイトの IP アドレスに設定する必要があります。

		> [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		> * **[Host]** 値が **www**、**[Points To]** 値が **&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> または
		> 
		> * **[Host]** 値が **awverify.www**、**[Points To]** 値が **awverify.&lt;yourwebsitename&gt;.azurewebsites.net**。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。


<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

