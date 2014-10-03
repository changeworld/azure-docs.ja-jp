ドメイン ネーム システム (DNS) は、インターネット上でリソースを検索するために使用されます。たとえば、ブラウザーにアドレスを入力するか、Web ページでリンクをクリックすると、DNS を使用してドメインが IP アドレスに変換されます。IP アドレスは住所に似ていますが、人間にとってわかりやすい情報ではありません。たとえば、**contoso.com** のような DNS 名の方が、192.168.1.88 や 2001:0:4137:1f67:24a2:3888:9cce:fea3 などの IP アドレスよりはるかに覚えやすい情報です。

DNS システムは*レコード*に基づいたしくみです。**contoso.com** のような特定の*名前*が、レコードによって IP アドレスまたは別の DNS 名に関連付けられます。Web ブラウザーなどのアプリケーションは、DNS で名前を検索し、レコードを検索して、そのレコードが指すアドレスを使用します。レコードが指す値が IP アドレスの場合、ブラウザーはその値を使用します。レコードが別の DNS 名を指す場合、アプリケーションは、もう一度名前を解決する必要があります。こうして最終的には、すべての名前が解決され、IP アドレスが取得されます。

Azure Web サイトを作成する場合、DNS 名は自動的にサイトに割り当てられます。この名前は、**\<yoursitename\>.azurewebsites.net** という形式です。また DNS レコードの作成時に仮想 IP アドレスを使用することもできるため、**.azurewebsites.net** を指すレコードを作成するか、または IP アドレスをポイントできます。

> [WACOM.NOTE] Web サイトを削除して再作成した場合や、基本モード、共有モード、または標準モードに設定されている Web サイトを無料モードに変更した場合は、IP アドレスが変更されます。

レコードにも複数の種類があり、それぞれに独自の機能と制約がありますが、Web サイトに関して重要なのは *A* レコードと *CNAME* レコードの 2 種類です。

### アドレス レコード (A レコード)

A レコードは、ドメイン (**contoso.com**、**www.contoso.com** など) または*ワイルドカード ドメイン* (**\*.contoso.com** など) を IP アドレスにマップします。Azure Web サイトの場合、サービスの仮想 IP または Web サイト用に購入した特定の IP アドレスです。

CNAME レコードと比較したときの A レコードの主な利点は次のとおりです。

-   **contoso.com** などのルート ドメインを IP アドレスにマップすることができます。多くのレジストラーでは、このようなマッピングには A レコードの使用が必須です。

-   ワイルドカードを使用したエントリ (**\*.contoso.com** など) を持つことができるため、複数のサブドメイン (**mail.contoso.com**、**blogs.contoso.com**、**www.contso.com** など) への要求を処理できます。

> [WACOM.NOTE] A レコードは静的 IP にマップされるため、変更を Web サイトの IP アドレスに自動的に解決することはできません。A レコードと共に使用する IP アドレスは、Web サイトのカスタム ドメイン名を設定する場合に提供されます。ただし、この値は Web サイトを削除または再作成する場合や Web サイトを無料モードに戻すように変更する場合に変更されることがあります。

### エイリアス レコード (CNAME レコード)

CNAME レコードは、**mail.contoso.com** や **www.contoso.com** などの*特定の* DNS 名をドメインを別の (正規の) ドメイン名にマップします。Azure Web サイトの場合、正規のドメイン名は、目的の Web サイトの **\<yoursitename\>.azurewebsites.net** ドメイン名です。CNAME を作成すると、**\<yoursitename\>.azurewebsites.net** ドメイン名のエイリアスが CNAME によって作成されます。 CNAME エントリは **\<yoursitename\>.azurewebsites.net** ドメイン名の IP アドレスを自動的に解決するため、Web サイトの IP アドレスが変更されても、特別な対応をする必要はありません。

> [WACOM.NOTE] いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (**contoso.com** など) ではなく、サブドメイン (**www.contoso.com** など) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、「[the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)][]」、または「[IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)][]」を参照してください。

### Azure Web サイトの DNS の詳細

Azure Web サイトで A レコードを使用するには、まず次の CNAME レコードの 1 つを作成する必要があります。

-   **ルート ドメインまたはワイルドカード サブドメインの場合** - **awverify** の DNS 名を **awverify.\<yourwebsitename\>.azurewebsites.net** にマッピング。

-   **特定のサブドメインの場合** - **awverify.\<sub-domain\>** の DNS 名を **awverify.\<yourwebsitename\>.azurewebsites.net** にマッピング。たとえば、A レコードが **blogs.contoso.com** に対応する場合には、**awverify.blogs** となります。

この CNAME レコードを使用して、目的のドメインを自分が所有していることを確認します。また、Web サイトの仮想 IP アドレスを指す A レコードを作成する必要があります。

IP アドレスだけでなく、Web サイトの **awverify** 名と **.azurewebsites.net** 名が、次の手順で検索できます。

1.  ブラウザーで、[Azure の管理ポータル][]を開きます。

2.  **[Web サイト]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、ページの下部にある **[ドメインの管理]** を選択します。

    ![][]

    > [WACOM.NOTE] **[ドメインの管理]** が有効ではない場合、使用しているのは無料 Web サイトです。無料 Web サイトではカスタム ドメイン名を使用できません。また、共有、基本、または標準モードにアップグレードする必要があります。サイトのモードの変更方法など、Web サイトのモードの詳細については、「[Web サイトの規模の設定方法][]」を参照してください。

3.  **[カスタム ドメインの管理]** ダイアログ ボックスに、**awverify** の情報、現在割り当てられている **.azurewebsites.net** ドメイン名、および仮想 IP アドレスが表示されます。DNS レコードを作成する際に使用するため、この情報を保存します。

    ![][1]

  [the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)]: http://en.wikipedia.org/wiki/CNAME_record
  [IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)]: http://tools.ietf.org/html/rfc1035
  [Azure の管理ポータル]: https://manage.windowsazure.com
  []: ./media/custom-dns-web-site/dncmntask-cname-6.png
  [Web サイトの規模の設定方法]: http://www.windowsazure.com/en-us/documentation/articles/web-sites-scale/
  [1]: ./media/custom-dns-web-site/managecustomdomains.png
