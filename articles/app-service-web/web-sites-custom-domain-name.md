<properties
	pageTitle="Azure アプリへのカスタム ドメイン名のマッピング"
	description="Azure App Service でカスタム ドメイン名 (バニティ ドメイン) をアプリにマップする方法について説明します。"
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
	ms.date="07/27/2016"
	ms.author="cephalin"/>

# Azure アプリへのカスタム ドメイン名のマッピング

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

この記事では、[Azure App Service](../app-service/app-service-value-prop-what-is.md) でカスタム ドメイン名を Web アプリ、モバイル アプリ バックエンド、または API アプリに手動でマップする方法について説明します。

アプリには、azurewebsites.net という固有のサブドメインが既に設定されています。たとえば、アプリの名前が **contoso** の場合、ドメイン名は **contoso.azurewebsites.net** になります。ただし、カスタム ドメイン名をアプリにマップして、URL に `www.contoso.com` のようにブランドが反映されるように設定できます。

>[AZURE.NOTE] [Azure フォーラム](https://azure.microsoft.com/support/forums/)では、Azure の専門家からアドバイスを得ることができます。より高いレベルのサポートを得るには、[Azure サポートのサイト](https://azure.microsoft.com/support/options/)で **[サポートの要求]** をクリックします。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## Azure ポータルで新しいカスタム ドメインを購入する

カスタム ドメイン名をまだ購入していない場合は、[Azure ポータル](https://portal.azure.com)のアプリの設定でカスタム ドメイン名を購入し、直接管理することができます。この方法なら、アプリで [Azure Traffic Manager](web-sites-traffic-manager-custom-domain-name.md) を使用するかどうかに関係なく、カスタム ドメインをアプリに簡単にマップできます。

手順については、[App Service でカスタム ドメイン名を購入する方法](custom-dns-web-site-buydomains-web-app.md)に関するページを参照してください。

## 外部で購入したカスタム ドメインをマップする

既に [Azure DNS](https://azure.microsoft.com/services/dns/) またはサード パーティ プロバイダーからカスタム ドメインを購入している場合に、カスタム ドメインをアプリにマップするには、3 つの主要な手順を実行します。

1. "[*(A レコードのみ)*" アプリの IP アドレスを取得します](#vip)。
2. [ドメインをアプリにマップする DNS レコードを作成します](#createdns)。
    - **場所**: ドメイン レジストラーの独自の管理ツール (例: Azure DNS、GoDaddy など)。
    - **理由**: ドメイン レジストラーによって目的のカスタム ドメインが Azure アプリに解決されるように設定する。
1. [Azure アプリのカスタム ドメイン名を有効にします](#enable)。
    - **場所**: [Azure ポータル](https://portal.azure.com)。
    - **理由**: カスタム ドメイン名に対する要求にアプリが応答するように設定する。
3. [DNS への反映を確認します](#verify)。

### マップできるドメインの種類

Azure App Service では、次のカテゴリのカスタム ドメインをアプリにマップできます。

- **ルート ドメイン** - ドメイン レジストラーに予約したドメイン名 (通常、`@` ホスト レコードによって表されます)。たとえば、**contoso.com** などのドメインです。
- **サブドメイン** - ルート ドメインの下にある任意のドメイン。たとえば、**www.contoso.com** (`www` ホスト レコードによって表されます)。同じルート ドメインの異なるサブドメインを Azure の異なるアプリにマップできます。
- **ワイルドカード ドメイン** - [左端の DNS ラベルが `*`](https://en.wikipedia.org/wiki/Wildcard_DNS_record) の任意のサブドメイン (たとえば、ホスト レコード `*`、`*.blogs`)。たとえば、***.contoso.com** などです。

### 使用できる DNS レコードの種類

必要に応じて、2 種類の標準 DNS レコードを使用してカスタム ドメインをマップできます。

- [A](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A) - カスタム ドメイン名を Azure アプリの仮想 IP アドレスに直接マップします。
- [CNAME](https://en.wikipedia.org/wiki/CNAME_record) - カスタム ドメイン名をアプリの Azure ドメイン名 (**&lt;*appname*>.azurewebsites.net**) にマップします。

CNAME の利点は、IP アドレスが変更されても維持されることです。アプリを削除して作成し直したり、より上位の価格レベルから **Shared** レベルに移行したりすると、アプリの仮想 IP アドレスが変更される可能性があります。そのような変更があった場合、A レコードは更新する必要がありますが、CNAME レコードは有効なままです。

このチュートリアルでは、A レコードを使用するための手順と CNAME レコードを使用するための手順を示します。

>[AZURE.IMPORTANT] ルート ドメインの CNAME レコード (つまり "ルート レコード") は作成しないようにしてください。詳細については、[CNAME レコードをルート ドメインで使用できない理由](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)に関するページを参照してください。ルート ドメインを Azure アプリにマップするには、CNAME レコードではなく A レコードを使用します。

<a name="vip"></a>
## 手順 1."*(A レコードのみ)*" アプリの IP アドレスを取得する
A レコードを使用してカスタム ドメイン名にマップするには、Azure アプリの IP アドレスが必要です。A レコードではなく CNAME レコードを使用してマップする場合は、この手順をスキップして次のセクションに移動してください。

1.	[Azure ポータル](https://portal.azure.com)にログインします。

2.	左側のメニューの **[App Services]** をクリックします。

4.	アプリをクリックし、**[カスタム ドメイン]** をクリックします。

6.  [Hostnames (ホスト名)] セクションの上の IP アドレスを書き留めます。

    ![Map custom domain name with A record: Get IP address for your Azure App Service app](./media/web-sites-custom-domain-name/virtual-ip-address.png)

7.  このポータルのブレードは開いたままにしておきます。DNS レコードを作成した後でこのブレードに戻ります。

<a name="createdns"></a>
## 手順 2.DNS レコードを作成する

ドメイン レジストラーにログインし、ツールを使用して A レコードまたは CNAME レコードを追加します。レジストラーの UI はレジストラーによって多少異なるため、プロバイダーのマニュアルを参照してください。ここでは、一般的なガイドラインを示します。

1.	DNS レコードの管理ページを探します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。通常、リンクを見つけるには、アカウント情報を表示し、**[ドメイン]** などのリンクを探します。
2.	DNS レコードを追加または編集するためのリンクを探します。これは、**[ゾーン ファイル]**、**[DNS レコード]** リンク、または **[Advanced (詳細)]** 構成リンクである場合があります。
3.  レコードを作成し、変更内容を保存します。
    - [A レコード向けの手順については、こちらを参照してください](#a)。
    - [CNAME レコード向けの手順については、こちらを参照してください](#cname)。

<a name="a"></a>
### A レコードを作成する

A レコードを使用して Azure アプリの IP アドレスにマップする場合、実際には A レコードと TXT レコードの両方を作成する必要があります。A レコードは DNS 解決自体のために使用され、TXT レコードはカスタム ドメイン名を所有していることを Azure が確認するために使用されます。

A レコードを次のように構成します (@ は通常、ルート ドメインを表します)。
 
<table cellspacing="0" border="1">
  <tr>
    <th>FQDN の例</th>
    <th>A ホスト</th>
    <th>A 値</th>
  </tr>
  <tr>
    <td>contoso.com (ルート)</td>
    <td>@</td>
    <td><a href="#vip">手順 1.</a> で取得した IP アドレス</td>
  </tr>
  <tr>
    <td>www.contoso.com (サブ)</td>
    <td>www</td>
    <td><a href="#vip">手順 1.</a> で取得した IP アドレス</td>
  </tr>
  <tr>
    <td>*.contoso.com (ワイルドカード)</td>
    <td>*</td>
    <td><a href="#vip">手順 1.</a> で取得した IP アドレス</td>
  </tr>
</table>

追加の TXT レコードでは、&lt;*subdomain*>.&lt;*rootdomain*> を &lt;*subdomain*>.azurewebsites.net にマップする規則が適用されます。TXT レコードを次のように構成します。

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN の例</th>
    <th>TXT ホスト</th>
    <th>TXT 値</th>
  </tr>
  <tr>
    <td>contoso.com (ルート)</td>
    <td>@</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>www.contoso.com (サブ)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (ワイルドカード)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="cname"></a>
### CNAME レコードを作成する

CNAME レコードを使用して Azure アプリの既定のドメイン名にマップする場合は、A レコードの場合と異なり、追加の CNAME レコードは不要です。

>[AZURE.IMPORTANT] ルート ドメインの CNAME レコード (つまり "ルート レコード") は作成しないようにしてください。詳細については、[CNAME レコードをルート ドメインで使用できない理由](http://serverfault.com/questions/613829/why-cant-a-cname-record-be-used-at-the-apex-aka-root-of-a-domain)に関するページを参照してください。ルート ドメインを Azure アプリにマップするには、CNAME レコードではなく [A レコード](#a)を使用します。

CNAME レコードを次のように構成します (@ は通常、ルート ドメインを表します)。

<table cellspacing="0" border="1">
  <tr>
    <th>FQDN の例</th>
    <th>CNAME ホスト</th>
    <th>CNAME 値</th>
  </tr>
  <tr>
    <td>www.contoso.com (サブ)</td>
    <td>www</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
  <tr>
    <td>*.contoso.com (ワイルドカード)</td>
    <td>*</td>
    <td>&lt;<i>appname</i>>.azurewebsites.net</td>
  </tr>
</table>

<a name="enable"></a>
## 手順 3.アプリのカスタム ドメイン名を有効にする

Azure ポータルの **[カスタム ドメイン]** ブレードに戻り ([手順 1.](#vip) を参照)、カスタム ドメインの完全修飾ドメイン名 (FQDN) を一覧に追加する必要があります。

1.	まだ [Azure ポータル](https://portal.azure.com)にログインしていない場合は、ログインします。

2.	Azure ポータルで、左側のメニューの **[App Services]** をクリックします。

3.	アプリをクリックし、**[カスタム ドメイン]**、**[Add hostname (ホスト名の追加)]** の順にクリックします。

4.	カスタム ドメインの FQDN を一覧に追加します (例: **www.contoso.com**)。

    ![Map a custom domain name to an Azure app: add to list of domain names](./media/web-sites-custom-domain-name/add-custom-domain.png)

    >[AZURE.NOTE] Azure により、ここで使用したドメイン名が確認されます。このドメイン名には、[手順 2.](#createdns) で DNS レコードを作成したのと同じドメイン名を必ず使用してください。

5.  **[Validate (検証)]** をクリックします。

6.  **[Validate (検証)]** をクリックすると、ドメインの検証ワークフローが開始されます。このワークフローは、ドメインの所有権に加えて、ホスト名を利用できるかどうかを確認して、結果 (成功またはエラー) を報告します。エラーの場合は、エラーの詳細とエラーの解決に役立つガイダンスが表示されます。

7.  検証が成功すると、**[Add hostname (ホスト名の追加)]** ボタンがアクティブになり、ホスト名を割り当てることができます。次に、ブラウザーでカスタム ドメイン名に移動します。実行中のアプリがカスタム ドメイン名を使用していることを確認できます。

8.  Azure による新しいカスタム ドメイン名の構成が完了したら、ブラウザーでカスタム ドメイン名に移動します。ブラウザーに Azure アプリが表示されます。これは、カスタム ドメイン名が正しく構成されていることを意味します。

<a name="verify"></a>
## DNS への反映を確認する

構成手順が終了した後、DNS プロバイダーによっては、変更が反映されるまでしばらく時間がかかることがあります。[http://digwebinterface.com/](http://digwebinterface.com/) を使用して、DNS への反映が想定どおりに行われていることを確認できます。サイトを参照してから、テキストボックスにホスト名を指定し、**[Dig]** をクリックします。結果を確認して、最新の変更が有効になっているかどうかを確認します。

![Map a custom domain name to an Azure app: verify DNS propagation](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] DNS エントリの反映には、最大 48 時間 (場合によってはそれ以上) かかります。すべてを正しく構成している場合でも、反映が正常に行われるまで待つ必要があります。

## 次のステップ
HTTPS でカスタム ドメイン名をセキュリティ保護する方法を確認します。そのためには、[Azure で SSL 証明書を購入](web-sites-purchase-ssl-web-site.md)するか、[別の場所からの SSL 証明書を使用](web-sites-configure-ssl-certificate.md)します。

>[AZURE.NOTE] Azure アカウントにサインアップする前に Azure App Service の使用を開始する場合は、[App Service の試用](http://go.microsoft.com/fwlink/?LinkId=523751)に関するページを参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。

[Azure DNS の概要](../dns/dns-getstarted-create-dnszone.md) [カスタム ドメインにおける Web アプリの DNS レコードの作成](../dns/dns-web-sites-custom-domain.md) [Azure DNS へのドメインの委任](../dns/dns-domain-delegation.md)


<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

<!---HONumber=AcomDC_0824_2016-->