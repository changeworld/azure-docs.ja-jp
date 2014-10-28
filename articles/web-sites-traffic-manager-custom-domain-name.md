<properties title="Learn how to configure an Azure website that uses Traffic Manager to use a custom domain name" pageTitle="Configure a custom domain name for an Azure website that uses Traffic Manager" metaKeywords="Windows Azure, Windows Azure Web Sites, domain name" description="" services="web-sites" documentationCenter="" authors="larryfr, jroth" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr, jroth"></tags>

# Traffic Manager を利用する Azure Website のカスタム ドメイン名の構成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="カスタム ドメイン" class="current">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">Network Solutions</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>

<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name/" title="Web サイト">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Traffic Manager を利用する Web サイト" class="current">Traffic Manager を利用する Web サイト</a></div>

[WACOM.INCLUDE [websites-cloud-services-css-guided-walkthrough][websites-cloud-services-css-guided-walkthrough]]

[WACOM.INCLUDE [intro][intro]]

この記事では、負荷分散に Traffic Manager を利用する Azure Websites でカスタム ドメイン名を使用する一般的な手順を示します。利用しているドメイン レジストラーがこの記事の上部にあるタブに表示されていることを確認してください。表示されている場合、ドメイン レジストラーに固有の手順については、その該当するタブをクリックしてください。

[WACOM.INCLUDE [tmwebsitefooter][tmwebsitefooter]]

[WACOM.INCLUDE [introfooter][introfooter]]

この記事の内容:

-   [DNS レコードについて][DNS レコードについて]
-   [Web サイトの標準モード用の構成][Web サイトの標準モード用の構成]
-   [カスタム ドメインの DNS レコードの追加][カスタム ドメインの DNS レコードの追加]
-   [Web サイトに対するトラフィック マネージャーの有効化][Web サイトに対するトラフィック マネージャーの有効化]

## <a name="understanding-records"></a>DNS レコードについて

[WACOM.INCLUDE [understandingdns][understandingdns]]

## <a name="bkmk_configsharedmode"></a>Web サイトの標準モード用の構成

[WACOM.INCLUDE [modes][modes]]

<a name="bkmk_configurecname"></a>

## カスタム ドメインの DNS レコードの追加

</p>
カスタム ドメインを Azure Website に関連付けるには、ドメイン名を購入したドメイン レジストラーのツールを使用して、新しいエントリをカスタム ドメインの DNS テーブルに追加する必要があります。次の手順を使用して DNS ツールを見つけて利用します。

1.  ドメイン レジストラーのアカウントにログオンし、DNS レコードの管理ページを探します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。多く場合、このページへのリンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。

2.  ドメイン名の管理ページが見つかったら、DNS レコードを編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

    -   このページには多くの場合、いくつかのレコードが既に作成されています。たとえば、<'**@**>' または '\*' を "ドメイン パーキング" ページに関連付けるエントリが既にあります。**www** のような一般的なサブドメインのレコードが含まれることもあります。
    -   このページには **CNAME レコード**が表示されるか、レコード タイプを選択するためのドロップダウンが表示されます。さらに、**A レコード**や **MX レコード**などの他のレコードが表示されることもあります。場合によっては、CNAME レコードは **Alias Record** などの別の名前で呼ばれています。
    -   このページには、**ホスト名**または**ドメイン名**から別のドメイン名に**マッピング**できるフィールドも表示されます。

3.  各レジストラーによって仕様が異なりますが、一般的には、カスタム ドメイン名 (**contoso.com** など) *から*、Azure Website に利用される Traffic Manager のドメイン名 (**contoso.trafficmanager.net** など) *に*マッピングします。

4.  レジストラーで DNS レコードの追加または変更が完了したら、変更を保存します。

## <a name="enabledomain"></a>Web サイトに対するトラフィック マネージャーの有効化

[WACOM.INCLUDE [modes][1]]

  [カスタム ドメイン]: /ja-jp/documentation/articles/web-sites-custom-domain-name "カスタム ドメイン"
  [GoDaddy]: /ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name "GoDaddy"
  [Network Solutions]: /ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name "Network Solutions"
  [Register.com]: /ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name "Register.com"
  [Enom]: /ja-jp/documentation/articles/web-sites-enom-custom-domain-name "Enom"
  [Moniker]: /ja-jp/documentation/articles/web-sites-moniker-custom-domain-name "Moniker"
  [Dotster]: /ja-jp/documentation/articles/web-sites-dotster-custom-domain-name "Dotster"
  [DomainDiscover]: /ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name "DomainDiscover"
  [Directnic]: /ja-jp/documentation/articles/web-sites-directnic-custom-domain-name "Directnic"
  [Web サイト]: /ja-jp/documentation/articles/web-sites-custom-domain-name/ "Web サイト"
  [Traffic Manager を利用する Web サイト]: /ja-jp/documentation/articles/web-sites-traffic-manager-custom-domain-name/ "Traffic Manager を利用する Web サイト"
  [websites-cloud-services-css-guided-walkthrough]: ../includes/websites-cloud-services-css-guided-walkthrough.md
  [intro]: ../includes/custom-dns-web-site-intro-traffic-manager.md
  [tmwebsitefooter]: ../includes/custom-dns-web-site-traffic-manager-notes.md
  [introfooter]: ../includes/custom-dns-web-site-intro-notes.md
  [DNS レコードについて]: #understanding-records
  [Web サイトの標準モード用の構成]: #bkmk_configsharedmode
  [カスタム ドメインの DNS レコードの追加]: #bkmk_configurecname
  [Web サイトに対するトラフィック マネージャーの有効化]: #enabledomain
  [understandingdns]: ../includes/custom-dns-web-site-understanding-dns-traffic-manager.md
  [modes]: ../includes/custom-dns-web-site-modes-traffic-manager.md
  [1]: ../includes/custom-dns-web-site-enable-on-traffic-manager.md
