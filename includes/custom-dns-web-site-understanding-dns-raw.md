ドメイン ネーム システム (DNS) は、インターネット上でリソースを検索するために使用されます。たとえば、ブラウザーに Web アプリのアドレスを入力するか、Web ページでリンクをクリックすると、DNS を使用してドメインが IP アドレスに変換されます。IP アドレスは住所に似ていますが、人間にとってわかりやすい情報ではありません。たとえば、**contoso.com** のような DNS 名の方が、192.168.1.88 や 2001:0:4137:1f67:24a2:3888:9cce:fea3 などの IP アドレスよりはるかに覚えやすい情報です。

DNS システムは*レコード*に基づいたしくみです。*contoso.com* のような特定の**名前**が、レコードによって IP アドレスまたは別の DNS 名に関連付けられます。Web ブラウザーなどのアプリケーションは、DNS で名前を検索し、レコードを検索して、そのレコードが指すアドレスを使用します。レコードが指す値が IP アドレスの場合、ブラウザーはその値を使用します。レコードが別の DNS 名を指す場合、アプリケーションは、もう一度名前を解決する必要があります。こうして最終的には、すべての名前が解決され、IP アドレスが取得されます。

DNS 名は、App Service で Web アプリを作成すると、その Web アプリに自動的に割り当てられます。この名前は、**&lt;yourwebappname&gt;.azurewebsites.net** という形式です。また DNS レコードの作成時に仮想 IP アドレスを使用することもできるため、**.azurewebsites.net** を指すレコードを作成するか、または IP アドレスをポイントできます。

> [AZURE.NOTE]Web アプリを削除して再作成した場合や、**Basic** モード、**Shared** モード、または **Standard** モードに設定されている Web アプリを **Free** モードに変更した場合は、IP アドレスが変更されます。

レコードにも複数の種類があり、それぞれに独自の機能と制約がありますが、Web アプリに関して重要なのは *A* レコードと *CNAME* レコードの 2 種類です。

###アドレス レコード (A レコード)

A レコードは、ドメイン (**contoso.com**、**www.contoso.com** など) または*ワイルドカード ドメイン* (***.contoso.com** など) を IP アドレスにマップします。App Service の Web アプリの場合、サービスの仮想 IP または Web アプリ用に購入した特定の IP アドレスです。

CNAME レコードと比較したときの A レコードの主な利点は次のとおりです。

* **contoso.com** などのルート ドメインを IP アドレスにマップすることができます。多くのレジストラーでは、このようなマッピングには A レコードの使用が必須です。

* ワイルドカードを使用したエントリ (***.contoso.com** など) を持つことができるため、複数のサブドメイン (**mail.contoso.com**、**blogs.contoso.com**、**www.contso.com** など) への要求を処理できます。

> [AZURE.NOTE]A レコードは静的 IP アドレスにマップされるため、変更を Web アプリの IP アドレスに自動的に解決することはできません。A レコードと共に使用する IP アドレスは、Web アプリのカスタム ドメイン名を設定する場合に提供されます。ただし、この値は Web アプリを削除または再作成する場合や App Service のプランを **Free** モードに戻す場合に変更されることがあります。

###エイリアス レコード (CNAME レコード)

CNAME レコードは、*mail.contoso.com* や **www.contoso.com** などの**特定の** DNS 名をドメインを別の (正規の) ドメイン名にマップします。App Service の Web アプリの場合、正規のドメイン名は、Web アプリの **&lt;yourwebappname>.azurewebsites.net** ドメイン名です。CNAME を作成すると、**&lt;yourwebappname>.azurewebsites.net** ドメイン名のエイリアスが CNAME によって作成されます。CNAME エントリは **&lt;yourwebappname>.azurewebsites.net** ドメイン名の IP アドレスに自動的に解決されるため、Web アプリの IP アドレスが変更されても、特別な対応を行う必要はありません。

> [AZURE.NOTE]いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (**contoso.com** など) ではなく、サブドメイン (**www.contoso.com** など) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、<a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME レコードに関するウィキペディアの項目</a>、または <a href="http://tools.ietf.org/html/rfc1035">IETF ドメイン名 - 実装と仕様書</a>を参照してください。

###Web アプリ DNS 固有情報

Web アプリで A レコードを使用するには、まず次の CNAME レコードの 1 つを作成する必要があります。

* **ルート ドメインまたはワイルドカード サブドメインの場合** - **awverify** の DNS 名を **awverify.&lt;yourwebappname&gt;.azurewebsites.net** にマッピング。

* **特定のサブドメインの場合** - **awverify.&lt;sub-domain>** の DNS 名を **awverify.&lt;yourwebappname&gt;.azurewebsites.net** にマッピング。たとえば、A レコードが **blogs.contoso.com** に対応する場合には、**awverify.blogs** となります。

この CNAME レコードを使用して、目的のドメインを自分が所有していることを確認します。また、Web アプリの仮想 IP アドレスを指す A レコードを作成する必要があります。

IP アドレスだけでなく、Web アプリの **awverify** 名と **.azurewebsites.net** 名が、次の手順で検索できます。

1. ブラウザーで、[Azure ポータル](https://portal.azure.com)を開きます。

2. **[Web Apps]** ブレードで、Web アプリの名前をクリックし、**[すべての設定]** を選択し、ページ下部の **[カスタム ドメインと SSL]** をクリックします。

	![](./media/custom-dns-web-site/dncmntask-cname-6.png)

	> [AZURE.NOTE]**[ドメインの管理]** が有効ではない場合、使用しているのは **Free** の Web アプリです。**Free** の Web アプリではカスタム ドメイン名を使用できません。また、App Service のプランを **Shared**、**Basic**、または **Standard**モードにアップグレードする必要があります。サイトのモードの変更方法など、App Service のプラン モードの詳細については、[Web アプリのスケールの設定方法](../articles/web-sites-scale.md)に関するページを参照してください。

6. **[カスタム ドメインの管理]** ダイアログ ボックスに、**awverify** の情報、現在割り当てられている **.azurewebsites.net** ドメイン名、および仮想 IP アドレスが表示されます。DNS レコードを作成する際に使用するため、この情報を保存します。

	![](./media/custom-dns-web-site/managecustomdomains.png)

<!---HONumber=July15_HO4-->