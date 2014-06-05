<properties title="カスタム ドメイン名を使用するように、トラフィック マネージャーを利用する Azure の Web サイトを構成する方法について説明します" pageTitle="トラフィック マネージャーを利用する Azure の Web サイトのカスタム ドメイン名の構成" metaKeywords="Windows Azure, Windows Azure の Web サイト, ドメイン名" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

#トラフィック マネージャーを利用する Azure の Web サイトのカスタム ドメイン名の構成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン" class="current">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="トラフィック マネージャーを利用する Web サイト" class="current">トラフィック マネージャーを利用する Web サイト</a></div>

[WACOM.INCLUDE [intro](../includes/custom-dns-web-site-intro-traffic-manager.md)]

この記事では、負荷分散にトラフィック マネージャーを利用する Azure の Web サイトでカスタム ドメイン名を使用する一般的な手順を示します。利用しているドメイン レジストラーがこの記事の上部にあるタブに表示されていることを確認してください。表示されている場合、ドメイン レジストラーに固有の手順については、その該当するタブをクリックしてください。

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

カスタム ドメインを Azure の Web サイトに関連付けるには、ドメイン名を購入したドメイン レジストラーのツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1. ドメイン レジストラーのアカウントにログオンし、DNS レコードの管理ページを探します。**[Domain Name]**、**[DNS]**、**[Name Server Management]** などのラベルが付いたサイトのリンクまたは領域を探します。多く場合、このページへのリンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。

4. ドメイン名の管理ページが見つかったら、DNS レコードを編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

	* このページには多くの場合、いくつかのレコードが既に作成されています。たとえば、'**@**' や '\*' をドメイン パーキング ページに関連付けるエントリが既にあります。**www** のような一般的なサブドメインのレコードが含まれることもあります。
	* このページには **CNAME レコード**が表示されるか、レコード タイプを選択するためのドロップダウンが表示されます。さらに、**A レコード**や **MX レコード**などの他のレコードが表示されることもあります。場合によっては、CNAME レコードは **Alias Record** などの別の名前で呼ばれています。
	* このページには、**ホスト名**や**ドメイン名**から別のドメイン名に**マッピング**できるフィールドも表示されます。

5. レジストラーによって仕様が異なりますが、一般的には、カスタム ドメイン名 (**contoso.com** など) *から*、Azure の Web サイトに利用されるトラフィック マネージャーのドメイン名 (**contoso.trafficmanager.net** など) *に*マッピングします。

6. レジストラーで DNS レコードの追加または変更が完了したら、変更を保存します。

<h2><a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化</h2>

[WACOM.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-traffic-manager.md)]

