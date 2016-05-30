<properties
	pageTitle="Azure App Service のカスタム ドメイン名の構成"
	description="Azure App Service の Web アプリでカスタム ドメイン名を使用する方法について説明します。"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="jimbe"
	tags="top-support-issue"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/16/2016"
	ms.author="cephalin"/>

# Azure App Service のカスタム ドメイン名の構成

> [AZURE.SELECTOR]
- [Web Apps のドメインを購入する](custom-dns-web-site-buydomains-web-app.md)
- [外部ドメインを使用する Web Apps](web-sites-custom-domain-name.md)
- [Traffic Manager 付きの Web アプリ](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

Web アプリを作成するときに、Azure は azurewebsites.net のサブドメインにそのアプリを割り当てます。たとえば、Web アプリの名前が **contoso** の場合、URL は **contoso.azurewebsites.net** になります。また Azure によって仮想 IP アドレスも割り当てられます。

運用 Web アプリでは、通常、カスタム ドメイン名をユーザーに表示します。この記事では、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) を使用してカスタム ドメインを構成する方法について説明します

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。または、Azure サポート インシデントを送信できます。その場合は、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)に移動して、**[サポートの要求]** をクリックします。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## 概要

外部ドメイン名をまだ登録していない (つまり、*.azurewebsites.net ではない) 場合、カスタム ドメインを設定する最も簡単な方法としては [Azure ポータル](https://portal.azure.com)で直接購入します。このプロセスでは、Web アプリのドメイン名を GoDaddy などのサードパーティ サイトにアクセスして管理する代わりに、ポータルで直接管理できます。同様に、Web アプリで [Azure Traffic Manager](web-sites-traffic-manager-custom-domain-name.md) を使用しているかどうかにかかわらず、Web アプリでのドメイン名の構成が大幅に簡略化されます。詳細については、[Azure App Service でのカスタム ドメイン名の購入と構成](custom-dns-web-site-buydomains-web-app.md)に関するページを参照してください。

既にドメイン名を持っている場合や、他のドメイン レジストラーからドメインを予約する場合、ここに示す手順は、Web アプリにカスタム ドメイン名を設定する一般的な手順です ([GoDaddy.com の特定の手順](web-sites-godaddy-custom-domain-name.md)を参照)。

1. ドメイン名を予約します。使用できるレジストラーの種類は数多く存在するため、ここでは、この手順については説明しません。サインアップすると、サイトによってプロセスが順に表示されます。
1. ドメインを Azure Web アプリにマップする DNS レコードを作成します。
1. [Azure ポータル](https://portal.azure.com)内でドメイン名を追加します。

基本的な手順は以上ですが、特に注意が必要な場合があります。

- ルート ドメインのマッピング。ルート ドメインとは、ドメイン レジストラーで予約したドメインです。たとえば、**contoso.com** などのドメインです。
- サブドメインのマッピング。たとえば、**blogs.contoso.com** はサブドメインです。複数のサブドメインをそれぞれ異なる Web アプリにマップできます。
- ワイルドカードのマッピング。たとえば、***.contoso.com** などです。ワイルドカードのエントリは、ドメインのすべてのサブドメインに適用されます。

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes.md)]


## DNS レコードの種類

ドメイン ネーム システム (DNS) では、データ レコードを使用してドメイン名が IP アドレスにマップされます。DNS レコードには、複数の種類があります。Web アプリの場合、*A* レコードまたは *CNAME* レコードを作成します。

- A **(Address)** レコードは、ドメイン名を IP アドレスにマップします。
- **CNAME (Canonical Name)** レコードは、ドメイン名を別のドメイン名にマップします。DNS は、2 番目の名前を使用してアドレスを検索します。その場合も、ユーザーのブラウザーには 1 番目のドメイン名が表示されます。たとえば、contoso.com を *&lt;yourwebapp&gt;*.azurewebsites.net にマップすることができます。

IP アドレスが変更された場合、CNAME エントリはそのまま有効ですが、A レコードは更新する必要があります。ただし、ドメイン レジストラーによっては、ルート ドメインやワイルドカード ドメインに CNAME レコードを使用できない場合があります。その場合は A レコードを使用する必要があります。

> [AZURE.NOTE] IP アドレスは、Web アプリを削除または再作成する場合や Web アプリを無料モードに戻すように変更する場合に変更されることがあります。


## 仮想 IP アドレスの検索

CNAME レコードを作成している場合は、この手順をスキップしてください。A レコードを作成するには、Web アプリの仮想 IP アドレスを見つける必要があります。IP アドレスを見つけるには:

1.	ブラウザーで、[Azure ポータル](https://portal.azure.com)を開きます。
2.	ページの左側にある **[参照]** オプションをクリックします。
3.	**[Web Apps]** ブレードをクリックします。
4.	Web アプリの名前をクリックします。
5.	**[Essentials]** ページで、**[すべての設定]** をクリックします。
6.	**[カスタム ドメインと SSL]** をクリックします。
7.	**[カスタム ドメインと SSL]** ブレードで **[外部ドメインの使用]** をクリックします。IP アドレスは、この部分の下部にあります。

## DNS レコードの作成

ドメイン レジストラーにログインし、ツールを使用して A レコードまたは CNAME レコードを追加します。レジストラーの Web アプリはすべて少しずつ異なりますが、一般的なガイドラインは次のとおりです。

1.	DNS レコードの管理ページを探します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。多く場合、リンクが見つかったら、アカウント情報を表示し、**[My domains]** などのリンクを探します。
2.	管理ページが見つかったら、DNS レコードを追加または編集できるリンクを探します。これは、**[Zone file]**、**[DNS Records]**、**[Advanced]** などの構成リンクとして表示されている場合があります。

このページには、A レコードと CNAME レコードが別々に表示されたり、レコードの種類を選択するためのドロップダウンが表示されたりする場合があります。あるいは、レコードの種類の名前として、A レコードの代わりに **IP アドレス レコード**、CNAME レコードの代わりに**エイリアス レコード**と表示されることもあります。通常、レジストラーではレコードが自動的に作成されるため、**www** などのルート ドメインや共通サブドメインのレコードが既に存在する場合があります。

レコードを作成または編集すると、フィールドでドメイン名を IP アドレス (A レコードの場合) または別のドメイン (CNAME レコードの場合) に割り当てられるようになります。CNAME レコードの場合、カスタム ドメインから azurewebsites.net サブドメインにマップします。

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
    <td>168.62.48.183</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (エイリアス)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

ここでカスタム ドメイン名を "contoso.com" とすると、次のレコードが作成されます。

- 168\.62.48.183 にマップされた **contoso.com**
- **contoso.azurewebsites.net** にマップされた **www.contoso.com**

>[AZURE.NOTE] Azure DNS を使用すると、Web アプリに必要なドメイン レコードをホストできます。Azure DNS でカスタム ドメインを構成し、レコードを作成するには、「[Web アプリのカスタム DNS レコードの作成](../dns/dns-web-sites-custom-domain.md)」をご覧ください。

<a name="awverify" />
## awverify レコードの作成 (A レコードのみ)

A レコードを作成する場合、使用しようとするドメインを自分が所有していることを確認するための特別な CNAME レコードも Web アプリに作成する必要があります。この CNAME レコードの形式は次のとおりです。

- *A レコードがルート ドメインまたはワイルドカード ドメインをマップする場合:* **awverify.&lt;yourdomain&gt;** から **awverify.&lt;yourwebappname&gt;.azurewebsites.net** にマップする CNAME レコードを作成します。たとえば、A レコードが **contoso.com** をマップする場合は、**awverify.contoso.com** をマップする CNAME レコードを作成します。
- *A レコードが特定のサブドメインをマップする場合:* **awverify.&lt;subdomain&gt;** から **awverify.&lt;yourwebappname&gt;.azurewebsites.net** にマップする CNAME レコードを作成します。たとえば、A レコードが **blogs.contoso.com** をマップする場合は、**awverify.blogs.contoso.com** をマップする CNAME レコードを作成します。

awverify サブドメインは、Web　アプリの訪問者に対して表示されることはなく、Azure がドメインの所有者を確認するためだけに使用されます。

## Web アプリケーションでのドメイン名の有効化

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

## DNS への反映を確認する

構成手順が終了した後、DNS プロバイダーによっては、変更が反映されるまでしばらく時間がかかることがあります。[http://digwebinterface.com/](http://digwebinterface.com/) を使用して、DNS への反映が想定どおりに行われていることを確認できます。サイトを参照してから、テキストボックスにホスト名を指定し、**[Dig]** をクリックします。結果を確認して、最新の変更が有効になっているかどうかを確認します。

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] DNS エントリの反映には、最大 48 時間 (場合によってはそれ以上) かかります。すべてを正しく構成している場合でも、反映が正常に行われるまで待つ必要があります。

## 次のステップ

詳細については、「[Azure DNS の概要](../dns/dns-getstarted-create-dnszone.md)」と「[Azure DNS へのドメインの委任](../dns/dns-domain-delegation.md)」をご覧ください。

## 変更内容
* Websites から App Service への変更ガイドについては、「[Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)」を参照してください。

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0518_2016-->