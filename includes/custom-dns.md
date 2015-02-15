# Azure クラウド サービスのカスタム ドメイン名の構成

> [AZURE.NOTE]
> より速く進める --新しい Azure の使用[チュートリアル ガイド](http://support.microsoft.com/kb/2990804)!Azure クラウド サービスまたは Azure Websites を使用したカスタム ドメイン名の関連付けおよび通信 (SSL) のセキュリティ保護がすばやく行えます。

Azure でアプリケーションを作成すると、cloudapp.net ドメイン上にサブドメインが提供されるため、ユーザーは http://&lt;*myapp*>.cloudapp.net のような URL を使用してアプリケーションにアクセスできるようになります。ただし、contoso.com のような独自のドメイン名を使用してアプリケーションを公開することもできます。

> [AZURE.NOTE] 
> このタスクの手順は、Azure Cloud Services に適用されます。ストレージ アカウントについては、「[Azure ストレージ アカウントの BLOB データのカスタム ドメイン名の構成](../storage-custom-domain-name/)」を参照してください。Web サイトについては、「[Azure の Web サイトのカスタム ドメイン名の構成](../web-sites-custom-domain-name/)」を参照してください。

この記事の内容:

-   [CNAME と A レコードについて](#access-app)
-   [カスタム ドメインの CNAME レコードの追加](#add-cname)
-   [カスタム ドメインの A レコードの追加](#add-aname)

<h2><a name="access-app"></a>CNAME と A レコードについて</h2>

CNAME レコード (またはエイリアス レコード) および A レコードはどちらもドメイン名を特定のサーバー (この場合、またはサービス) に関連付けることができますが、機能は異なります。また、Azure のクラウド サービスで A レコードを使用する場合には固有の考慮事項もいくつかあるため、どちらのレコードを使用するかを決定する前に、これらの点を検討しておく必要があります。

###CNAME レコードまたはエイリアス レコード

CNAME レコードは、**contoso.com** や **www.contoso.com** などの *specific*ドメインを正規のドメイン名にマップします。この場合、正規のドメイン名は Azure ホステッド アプリケーションの **&lt;myapp>.cloudapp.net** ドメイン名です。作成すると、CNAME は **&lt;myapp>.cloudapp.net** のエイリアスを作成します。CNAME エントリは **&lt;myapp>.cloudapp.net** サービスの IP アドレスを自動的に解決するため、クラウド サービスの IP アドレスが変更されても、特別な対応をする必要はありません。

> [AZURE.NOTE] 
> いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (contoso.com など) ではなく、サブドメイン (www.contoso.com) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、<a href="http://en.wikipedia.org/wiki/CNAME_record">CNAME レコードに関するウィキペディア項目</a>、または「<a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)</a>」を参照してください。

###A レコード

A レコードは、ドメイン (**contoso.com**、**www.contoso.com** など)  *or a wildcard domain* (**\*.contoso.com** など) を IP アドレスにマップします。Azure のクラウド サービスの場合は、サービスの仮想 IP です。CNAME レコードと比較したときの A レコードの主な利点は、ワイルドカードを使用する 1 つのエントリを使用することができて (***.contoso.com** など)、複数のサブドメイン (**mail.contoso.com**、**login.contoso.com**、**www.contso.com** など) の要求を処理できることです。

> [AZURE.NOTE]
> A レコードは静的 IP にマップされるため、変更をクラウド サービスの IP アドレスに自動的に解決することはできません。クラウド サービスによって使用される IP アドレスは、空のスロット (運用またはステージング) に初めて展開したときに割り当てられます。スロットの展開を削除すると、IP アドレスは Azure によって解放され、そのスロットへの今後の展開は新しい IP アドレスに与えられます。
> 
> 都合が良いのは、ステージング展開と運用展開との間のスワッピングまたは既存の展開のインプレース アップグレードを実行する場合に、所定の展開スロット (運用またはステージング) の IP アドレスが保持されることです。これらの操作の実行の詳細については、次の情報を参照してください: [クラウド サービスの管理方法](../cloud-services-how-to-manage/).


<h2><a name="add-cname"></a>カスタム ドメインの CNAME レコードの追加</h2>

CNAME レコードを作成するには、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加する必要があります。それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. これらの手段のいずれかを使用して、クラウド サービスに割り当てられた **.cloudapp.net** ドメイン名を見つけます。

  * [Azure の管理ポータル]にログインし、クラウド サービスを選択して、**[ダッシュボード]** を選択し、**[概要]** セクションの **[サイトの URL]** エントリを見つけます。

  		  ![サイトの URL を表示する [概要] セクション][csurl]

  * [Azure Powershell](../install-configure-powershell/)をインストールして構成し、次のコマンドを使用します。

    Get-AzureDeployment -ServiceName yourservicename | Select Url

  CNAME レコードを作成する場合に必要になるため、いずれかの方法で返された URL で使用されているドメイン名を保存します。

1.  DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

2.  ここで CNAME レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。**CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

3.  また、**www.customdomain.com** のエイリアスを作成する場合は、CNAME のドメインまたはサブドメイン エイリアス (**www** など) を指定する必要があります。ルート ドメインのエイリアスを作成する場合は、レジストラーの DNS ツールに '**@**' シンボルとして示される場合があります。

4. 次に、正規のホスト名 (ここではアプリケーションの **cloudapp.net** ドメイン) を指定します。

たとえば、次の CNAME レコードでは、すべてのトラフィックが **www.contoso.com** から、展開されたアプリケーションのカスタム DNS 名である **contoso.cloudapp.net** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
<td><strong>正規のドメイン</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.cloudapp.net</td>
</tr>
</table>

**www.contoso.com** の利用者は、本当のホストである
(contoso.cloudapp.net) を認識しないため、転送プロセスは
エンド ユーザーに表示されません。

> [AZURE.NOTE]
> 上の例は、<strong>www</strong> サブドメインのトラフィックのみに該当します。CNAME レコードにはワイルドカードを使用できないため、各ドメインおよびサブドメインに 1 つの CNAME を作成する必要があります。サブドメイン (*.contoso.com など)からトラフィックを cloudapp.net アドレスに転送するには、DNS 設定の <strong>URL リダイレクト</strong> エントリまたは <strong>URL 転送</strong>エントリを構成するか、A レコードを作成します。


<h2><a name="add-aname"></a>カスタム ドメインの A レコードの追加</h2>

A レコードを作成するには、まず、クラウド サービスの仮想 IP アドレスを見つける必要があります。次に、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加します。それぞれのレジストラーでは A レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. 次の手段のいずれかを使用して、クラウド サービスの IP アドレスを取得します。

  * [Azure の管理ポータル]にログインし、クラウド サービスを選択して、**[ダッシュボード]** を選択し、**[概要]** セクションの **[パブリック仮想 IP (VIP) アドレス]** エントリを見つけます。

   		 ![VIP を表示する [概要] セクション][vip]

  * [Azure Powershell](../install-configure-powershell/)をインストールして構成し、次のコマンドを使用します。

      get-azurevm -servicename yourservicename | get-azureendpoint -VM {$_.VM} | select Vip

    複数のエンドポイントがクラウド サービスに関連付けられている場合は、IP アドレスを含んだ複数の行を受け取りますが、すべての行に同じアドレスが表示されます。

  A レコードを作成する場合に必要になるため、IP アドレスを保存します。

1.  DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

2.  ここで A レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。

3. この A レコードを使用するドメインまたはサブドメインを選択または入力します。たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。すべてのサブドメインのワイルドカード エントリを作成する場合は、「'__*__'.」と入力します。これは、**mail.customdomain.com**、**login.customdomain.com**、**www.customdomain.com** などすべてのサブドメインを対象とします。

  ルート ドメインに A レコードを作成する場合は、レジストラーの DNS ツールに **@**' シンボルとして示される場合があります。

4. 表示されたフィールドのクラウド サービスの IP アドレスを入力します。これによって、A レコードで使用されるドメイン エントリがクラウド サービスの展開の IP アドレスに関連付けられます。

たとえば、次の A レコードでは、すべてのトラフィックが **contoso.com** から、展開されたアプリケーションの IP アドレス名である **137.135.70.239** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>ホスト名/サブドメイン</strong></td>
<td><strong>IP アドレス</strong></td>
</tr>
<tr>
<td>@</td>
<td>137.135.70.239</td>
</tr>
</table>

この例では、ルート ドメインの A レコードを作成する方法を示します。すべてのサブドメインを対象とするワイルドカードを作成する場合は、サブドメインとして「'__*__'」と入力します。

## 次のステップ

-   [クラウド サービスの管理方法](../cloud-services-how-to-manage/)
-   [CDN コンテンツをカスタム ドメインにマッピングする方法][]

  [アプリケーションをカスタム ドメイン上で公開する]: #access-app
  [カスタム ドメインの CNAME レコードの追加]: #add-cname
  [データをカスタム ドメイン上で公開する]: #access-data
  [VIP スワップ]: http://msdn.microsoft.com/ja-jp/library/ee517253.aspx
  [サブドメインをストレージ アカウントに関連付ける CNAME レコードを作成する]: #create-cname
  [Azure 管理ポータル]: https://manage.windowsazure.com
  [カスタム ドメインの検証のダイアログ ボックス]: http://i.msdn.microsoft.com/dynimg/IC544437.jpg
  [CDN コンテンツをカスタム ドメインにマッピングする方法]: http://msdn.microsoft.com/ja-jp/library/windowsazure/gg680307.aspx
  [vip]: ./media/custom-dns/csvip.png
  [csurl]:./media/custom-dns/csurl.png
<!--HONumber=42-->
