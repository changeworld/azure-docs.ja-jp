<properties 
	pageTitle="Azure の Web サイトのカスタム ドメイン名の構成" 
	description="Azure の Web サイトのカスタム ドメイン名を作成する方法について説明します。" 
	services="web-sites" 
	documentationCenter="" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr, jroth, mwasson"/>

#Azure Web サイトのカスタム ドメイン名の構成

<div class="dev-center-tutorial-selector sublanding"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name" title="Custom Domain" class="current">カスタム ドメイン</a><a href="/ja-jp/documentation/articles/web-sites-godaddy-custom-domain-name" title="GoDaddy">GoDaddy</a><a href="/ja-jp/documentation/articles/web-sites-network-solutions-custom-domain-name" title="Network Solutions">ネットワーク ソリューション</a><a href="/ja-jp/documentation/articles/web-sites-registerdotcom-custom-domain-name" title="Register.com">Register.com</a><a href="/ja-jp/documentation/articles/web-sites-enom-custom-domain-name" title="Enom">Enom</a><a href="/ja-jp/documentation/articles/web-sites-moniker-custom-domain-name" title="Moniker">Moniker</a><a href="/ja-jp/documentation/articles/web-sites-dotster-custom-domain-name" title="Dotster">Dotster</a><a href="/ja-jp/documentation/articles/web-sites-domaindiscover-custom-domain-name" title="DomainDiscover">DomainDiscover</a><a href="/ja-jp/documentation/articles/web-sites-directnic-custom-domain-name" title="Directnic">Directnic</a></div>
<div class="dev-center-tutorial-subselector"><a href="/ja-jp/documentation/articles/web-sites-custom-domain-name/" title="Websites" class="current">Web サイト</a> | <a href="/ja-jp/documentation/articles/web-sites-traffic-manager-custom-domain-name/" title="Website using Traffic Manager">Traffic Manager を使用する Web サイト</a></div>

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Web サイトを作成すると、Azure はこれを azurewebsites.net のサブドメインに割り当てます。たとえば、**contoso** という名前を Web サイトに付けると、URL は **contoso.azurewebsites.net** となります。また Azure によって仮想 IP アドレスも割り当てられます。

![contoso.azurewebsites.net subdomain][subdomain]

運用 Web サイトでは、通常、カスタム ドメイン名をユーザーに表示します。この記事では、Azure Web サイトを使用してカスタム ドメインを構成する方法について説明します(この記事では、すべてのドメイン レジストラーに共通する一般的な手順を示します。特定のレジストラーについては、この記事の上部にあるタブをクリックしてください)。

[AZURE.INCLUDE [introfooter](../includes/custom-dns-web-site-intro-notes.md)]

この記事の内容:


-   [概要]
-   [DNS レコードの種類]
-   [仮想 IP アドレスの検索]
-   [DNS レコードの作成]
-   ["awverify" レコードの作成 (A レコードのみ)](#awverify)
-   [Web サイトでのドメイン名の有効化]


## 概要

カスタム ドメイン名を構成するための一般的な手順は次のとおりです。

1. ドメイン名を予約します。使用できるレジストラーの種類は数多く存在するため、ここでは、この手順については説明しません。サインアップすると、サイトによってプロセスが順に表示されます。
1. ドメインを Azure Web サイトにマップする DNS レコードを作成します。 
1. Azure の管理ポータル内でドメイン名を追加します。 

基本的な手順は以上ですが、特に注意が必要な場合があります。

- ルート ドメインのマッピング。ルート ドメインとは、ドメイン レジストラーで予約したドメインです。たとえば、**contoso.com** などのドメインです。
- サブドメインのマッピング。たとえば、**blogs.contoso.com** はサブドメインです。複数のサブドメインをそれぞれ異なる Web サイトにマップできます。
- ワイルドカードのマッピング。たとえば、***.contoso.com** などです。ワイルドカードのエントリは、ドメインのすべてのサブドメインに適用されます。 
 
[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-modes.md)]


## DNS レコードの種類

ドメイン ネーム システム (DNS) では、データ レコードを使用してドメイン名が IP アドレスにマップされます。DNS レコードには、複数の種類があります。Web サイトの場合、*A* レコードまたは *CNAME* レコードを作成します。

- A **(Address)** レコードは、ドメイン名を IP アドレスにマップします。 
- **CNAME (Canonical Name)** レコードは、ドメイン名を別のドメイン名にマップします。DNS は、2 番目の名前を使用してアドレスを検索します。その場合も、ユーザーのブラウザーには 1 番目のドメイン名が表示されます。たとえば、contoso.com を *&lt;yoursite&gt;*.azurewebsites.net にマップできます。 

IP アドレスが変更された場合、CNAME エントリはそのまま有効ですが、A レコードは更新する必要があります。ただし、ドメイン レジストラーによっては、ルート ドメインやワイルドカード ドメインに CNAME レコードを使用できない場合があります。その場合は A レコードを使用する必要があります。 

> [AZURE.NOTE] IP アドレスは、Web サイトを削除または再作成する場合や Web サイトを無料モードに戻すように変更する場合に変更されることがあります。


## 仮想 IP アドレスの検索 

CNAME レコードを作成している場合は、この手順をスキップしてください。A レコードを作成するには、Web サイトの仮想 IP アドレスを見つける必要があります。IP アドレスを見つけるには:

1.	ブラウザーで、[Azure の管理ポータル](https://manage.windowsazure.com)を開きます。
2.	**[Web サイト]** タブでサイト名をクリックし、**[ダッシュボード]** を選択します。
3.	ページの下部にある **[ドメインの管理]** をクリックします(このオプションが無効な場合は、共有、基本、または標準モードを使用していることを確認します。詳細については、「[Web サイトの規模の設定方法](http://www.windowsazure.com/ja-jp/documentation/articles/web-sites-scale/)」をご覧ください)。 

	![](media/web-sites-custom-domain-name/dncmntask-cname-6.png)

4.	IP アドレスが、ダイアログの下部に表示されます。

	![](media/web-sites-custom-domain-name/ipaddress.png)

## DNS レコードの作成

ドメイン レジストラーにログインし、ツールを使用して A レコードまたは CNAME レコードを追加します。レジストラーの Web サイトはすべて少しずつ異なりますが、一般的なガイドラインは次のとおりです。 

1.	DNS レコードの管理ページを探します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。多くの場合、リンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。
2.	管理ページが見つかったら、DNS レコードを追加または編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

このページには、A レコードと CNAME レコードが別々に表示されたり、レコードの種類を選択するためのドロップダウンが表示されたりする場合があります。あるいは、レコードの種類の名前として、A レコードの代わりに **IP アドレス レコード**、CNAME レコードの代わりに**エイリアス レコード**と表示されることもあります。通常、レジストラーではレコードが自動的に作成されるため、ルート ドメインや **www** などの共通サブドメインのレコードが既に存在する場合があります。

レコードを作成または編集すると、フィールドでドメイン名を IP アドレス (A レコードの場合) または別のドメイン (CNAME レコードの場合) に割り当てられるようになります。CNAME レコードの場合、カスタム ドメイン*from* azurewebsites.net サブドメイン*to*マップします。 

多くのレジストラー ツールでは、ドメイン名すべてではなく、ドメインのサブドメイン部分のみを入力します。また多くのツールでは、ルート ドメインを "@" で表します。次に例を示します。

<table cellspacing="0" border="1">
<tr>
	<th>ホスト</th>
	<th>レコードの種類</th>
	<th>IP アドレスまたは URL</th>
</tr>
<tr>
	<td>@</td>
	<td>A (アドレス)</td>
	<td>127.0.0.1</td>
</tr>
<tr>
	<td>www</td>
	<td>CNAME (エイリアス)</td>
	<td>contoso.azurewebsites.net</td>
</tr>
</table>

ここでカスタム ドメイン名を 'contoso.com' とすると、次のレコードが作成されます。

- 127.0.0.1 にマップされた **contoso.com**
- **contoso.azurewebsites.net** にマップされた **www.contoso.com**


<h2 id="awverify">"awverify" レコードの作成 (A レコードのみ)</h2>

A レコードを作成する場合、使用しようとするドメインを自分が所有していることを確認するための特別な CNAME レコードも Azure の Web サイトに作成する必要があります。この CNAME レコードの形式は次のとおりです。 

- *A レコードがルート ドメインまたはワイルドカード ドメインをマップする場合:* **awverify.&lt;yourdomain&gt;** から **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** にマップする CNAME レコードを作成します。たとえば、A レコードが **contoso.com** 用の場合は、**awverify.contoso.com** 用の CNAME レコードを作成します。
- *A レコードが特定のサブドメインをマップする場合:* **awverify.&lt;subdomain&gt;** から **awverify.&lt;yourwebsitename&gt;.azurewebsites.net** にマップする CNAME レコードを作成します。たとえば、A レコードが **blogs.contoso.com** 用の場合は、**awverify.blogs.contoso.com** 用の CNAME レコードを作成します。

awverify サブドメインは、サイトの訪問者に対して表示されることはなく、Azure がドメインの所有者を確認するためだけに使用されます。

## Web サイトでのドメイン名の有効化

[AZURE.INCLUDE [modes](../includes/custom-dns-web-site-enable-on-web-site.md)]


<!-- Anchors. -->
[概要]: #overview
[DNS レコードの種類]: #dns-record-types
[仮想 IP アドレスの検索]: #find-the-virtual-ip-address
[DNS レコードの作成]: #create-the-dns-records
[Web サイトでのドメイン名の有効化]: #enable-the-domain-name-on-your-website

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!--HONumber=42-->
