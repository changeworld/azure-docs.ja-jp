ドメイン ネーム システム (DNS) は、インターネット上で要素を検索するために使用されます。たとえば、ブラウザーにアドレスを入力するか、Web ページでリンクをクリックすると、DNS を使用してドメインが IP アドレスに変換されます。IP アドレスは住所に似ていますが、人間にとってわかりやすい情報ではありません。たとえば、**contoso.com** のような DNS 名の方が、192.168.1.88 や 2001:0:4137:1f67:24a2:3888:9cce:fea3 などの IP アドレスよりはるかに覚えやすい情報です。

DNS システムは*レコード*に基づいたしくみです。**contoso.com** のような特定の*名前*が、レコードによって IP アドレスまたは別の DNS 名に関連付けられます。Web ブラウザーなどのアプリケーションは、DNS で名前を検索し、レコードを検索して、そのレコードが指すアドレスを使用します。レコードが指す値が IP アドレスの場合、ブラウザーはその値を使用します。レコードが別の DNS 名を指す場合、アプリケーションは、もう一度名前を解決する必要があります。こうして最終的には、すべての名前が解決され、IP アドレスが取得されます。

DNS 名は、Azure Web サイトの作成時に、サイトに自動的に割り当てられます。この名前は、**\<yoursitename\>.azurewebsites.net** という形式です。Web サイトを Azure Traffic Manager エンドポイントとして追加すると、**\<yourtrafficmanagerprofile\>.trafficmanager.net** ドメイン経由で Web サイトにアクセスできます。

> [WACOM.NOTE] Web サイトが Traffic Manager エンドポイントとして構成されている場合、DNS レコードの作成時に **.trafficmanager.net** アドレスを使用します。

> [WACOM.NOTE] Traffic Manager では、CNAME レコードのみ使用できます。

レコードにも複数の種類があり、それぞれに独自の機能と制約がありますが、Traffic Manager エンドポイントとして構成された Web サイトに関して重要なのは *CNAME* レコードのみです。

### CNAME レコードまたはエイリアス レコード

CNAME レコードは、**mail.contoso.com** や **www.contoso.com** などの*特定の* DNS 名をドメインを別の (正規の) ドメイン名にマップします。Traffic Manager を使用した Azure Web サイトの場合、正規のドメイン名は、Traffic Manager プロファイルの **\<myapp\>.trafficmanager.net** ドメイン名です。CNAME を作成すると、**\<myapp\>.trafficmanager.net** ドメイン名のエイリアスが CNAME によって作成されます。CNAME エントリは **\<myapp\>.trafficmanager.net** ドメイン名の IP アドレスを自動的に解決するため、Web サイトの IP アドレスが変更されても、特別な対応を行う必要はありません。

Traffic Manager に到着したトラフィックは、構成された負荷分散方法を使用して、Web サイトにルーティングされます。これは、Web サイトにアクセスするユーザーに対して透過的です。ユーザーのブラウザーには、カスタム ドメイン名のみが表示されます。

> [WACOM.NOTE] いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (**contoso.com** など) ではなく、サブドメイン (**www.contoso.com** など) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、「[the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)][]」、または「[IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)][]」を参照してください。

  [the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)]: http://en.wikipedia.org/wiki/CNAME_record
  [IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)]: http://tools.ietf.org/html/rfc1035
