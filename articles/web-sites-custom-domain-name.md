<properties title="カスタム ドメイン名を使用するように Azure の Web サイトを構成する方法について説明します" pageTitle="Azure の Web サイトのカスタム ドメイン名の構成" metaKeywords="Azure, Azure の Web Sites, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#Azure の Web Site のカスタム ドメイン名の構成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン" class="current">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name/" title="Web Sites" class="current">Web Site</a> | <a href="/ja-jp/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web Site">Traffic Manager を利用する Web Site</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro.md)]

この記事では、カスタム ドメイン名を Azure の Web Sites で使用する一般的な手順を示します。利用しているドメイン レジストラーがこの記事の上部にあるタブに表示されていることを確認してください。表示されている場合、ドメイン レジストラーに固有の手順については、その該当するタブをクリックしてください。

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

カスタム ドメインを Azure の Web Site に関連付けるには、ドメイン名を購入したドメイン レジストラーのツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. ドメイン レジストラーのアカウントにログオンし、DNS レコードの管理ページを探します。**[Domain Name]**、**[DNS]**、**[Name Server Management]** などのラベルが付いたサイトのリンクまたは領域を探します。多く場合、このページへのリンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。

4. ドメイン名の管理ページが見つかったら、DNS レコードを編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

	* このページには多くの場合、いくつかのレコードが既に作成されています。たとえば、'**@**' や '\*' をドメイン パーキング ページに関連付けるエントリが既にあります。**www** のような一般的なサブドメインのレコードが含まれることもあります。
	* このページには **A レコード**と **CNAME レコード**が表示されるか、レコードの種類を選択するためのドロップダウンが表示されます。さらに、**MX レコード**などの他のレコードが表示されることもあります。これらは、A レコードの代わりに **IP アドレス レコード**、CNAME レコードの代わりに **エイリアス レコード** などの他の名前で呼ばれる場合があります。
	* このページには、**ホスト名**や**ドメイン名**から **IP Address** や別のドメイン名に**マッピング**できるフィールドも表示されます。

5. レジストラーによって仕様が異なりますが、一般的には、カスタム ドメイン名 (**contoso.com** など) *から*、Azure の Web Site のドメイン名 (**contoso.trafficmanager.net**) または Azure の Web Site の仮想 IP アドレス*に*マッピングします。

	* CNAME レコードは常に、Azure の Web Sites ドメイン - **contoso.azurewebsites.net** にマッピングされます。そのため、**www** などのドメイン*から* **&lt;yourwebsitename&gt;.azurewebsites.net** アドレス*に*マッピングされます。
	
		> [WACOM.NOTE] A レコードを使用する場合、次のいずれかの構成で CNAME レコードを追加する必要があります。
		> 
		>  * **www** *から* **&lt;yourwebsitename&gt;.azurewebsites.net** *へ*マッピングします。
		> 
		> または
		> 
		> * **awverify.www** *から* **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** *へ*マッピングします。
		> 
		> この CNAME レコードは Azure で使用されて、A レコードで参照されるドメインの所有者が検証されます。

	* A レコードは常に、Azure の Web Sites の仮想 IP アドレスにマッピングされます。そのため、**www** などのドメイン*から* Web サイトの仮想 IP アドレス*に*マッピングされます。
	
		> [WACOM.NOTE] ルート ドメイン (**contoso.com** など) を Azure の Web Site にマッピングするには、通常、'**@**' から、または空のエントリから仮想 IP アドレスにマッピングします。すべてのサブドメインを仮想 IP アドレスにマッピングするワイルドカード マッピングを作成するには、通常、'*' から仮想 IP アドレスにマッピングします。
		> 
		> ルートまたはワイルドカードのマッピングの詳細はレジストラーによって変わります。特定のガイダンスの詳細については、レジストラーで提供されるドキュメントを参照してください。

6. レジストラーで DNS レコードの追加または変更が完了したら、変更を保存します。

<h2><a name="enabledomain"></a>Web サイトでのドメイン名の有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]

