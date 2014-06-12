#Azure の Web サイトのカスタム ドメイン名の構成

Azure でアプリケーションを作成すると、azurewebsites.net ドメイン上にフレンドリ サブドメインが提供されるため、ユーザーは http://&lt;mysite>.azurewebsites.net のような URL を使用して Web サイトにアクセスできるようになります。ただし、Web サイトを共有モードまたは標準モード用に構成すると、Web サイトを独自のドメイン名にマップできます。

必要に応じて、Azure の Traffic Manager を使用して Web サイトへの着信トラフィックを負荷分散できます。Traffic Manager の Web Sites での使用の詳細については、「[Azure の Traffic Manager による Azure の Web Sites のトラフィックの制御][trafficmanager]」を参照してください。

> [WACOM.NOTE]このタスクの手順は、Azure の Web Sites に適用されます。Cloud Services については、「<a href="http://www.windowsazure.com/ja-jp/develop/net/common-tasks/custom-dns/">Configuring a Custom Domain Name in Azure (Azure でのカスタム ドメイン名の構成)</a>」を参照してください。

> [WACOM.NOTE] このタスクの手順は、Web サイトを共有モードまたは標準モードに構成することを必要とします。サブスクリプションに対して課金される金額が変更される可能性があります。詳細については、「<a href="http://www.windowsazure.com/ja-jp/pricing/details/web-sites/">Web サイトの料金詳細</a>」を参照してください。

この記事の内容:

-   [CNAME レコードと A レコードについて](#understanding-records)
-   [Web サイトの共有または標準モード用の構成](#bkmk_configsharedmode)
-   [Web サイトの Traffic Manager への追加](#trafficmanager)
-   [カスタム ドメインの CNAME レコードの追加](#bkmk_configurecname)
-   [カスタム ドメインの A レコードの追加](#bkmk_configurearecord)

<h2><a name="understanding-records"></a>CNAME レコードと A レコードについて</h2>

CNAME レコード (またはエイリアス レコード) および A レコードはどちらもドメイン名を Web サイトに関連付けることができますが、それぞれ機能は異なります。

###CNAME レコードまたはエイリアス レコード

CNAME レコードは、**contoso.com** や **www.contoso.com** などの*特定の*ドメインを正規のドメイン名にマップします。この場合、正規のドメイン名は Azure の Web サイトの **&lt; myapp>.azurewebsites.net** ドメイン名または Traffic Manager プロファイルの **&lt;myapp>.trafficmgr.com** ドメイン名です。CNAME を作成すると、**&lt;myapp>.azurewebsites.net** ドメイン名または **&lt;myapp>.trafficmgr.com** ドメイン名のエイリアスが CNAME によって作成されます。CNAME エントリは **&lt;myapp>.azurewebsites.net** ドメイン名または **&lt;myapp>.trafficmgr.com** ドメイン名の IP アドレスを自動的に解決するため、Web サイトの IP アドレスが変更されても、特別な対応を行う必要はありません。

> [WACOM.NOTE] いくつかのドメイン レジストラーでは、CNAME レコードを使用する場合にマップすることが許可されるのは、ルート名 (contoso.com など) ではなく、サブドメイン (www.contoso.com など) のみです。CNAME レコードの詳細については、レジストラーが提供するドキュメント、「<a href="http://en.wikipedia.org/wiki/CNAME_record">the Wikipedia entry on CNAME record (CNAME レコードに関するウィキペディア項目)</a>」、または「<a href="http://tools.ietf.org/html/rfc1035">IETF Domain Names - Implementation and Specification (IETF ドメイン名 - 実装と仕様書)</a>」を参照してください。

###A レコード

A レコードは、ドメイン (**contoso.com**、**www.contoso.com** など) または*ワイルドカード ドメイン* (**\*.contoso.com** など) を IP アドレスにマップします。Azure の Web Site の場合、サービスの仮想 IP または Web サイト用に購入した特定の IP アドレスです。CNAME レコードと比較したときの A レコードの主な利点は、ワイルドカードを使用する 1 つのエントリを使用することができて (***.contoso.com** など)、複数のサブドメイン (**mail.contoso.com**、**login.contoso.com**、**www.contso.com** など) の要求を処理できることです。

> [WACOM.NOTE]A レコードは静的 IP にマップされるため、変更を Web サイトの IP アドレスに自動的に解決することはできません。A レコードと共に使用する IP アドレスは、Web サイトのカスタム ドメイン名を設定する場合に提供されます。ただし、この値は Web サイトを削除または再作成する場合や Web サイトを無料モードに戻すように変更する場合に変更されることがあります。

> [WACOM.NOTE] A レコードは Traffic Manager での負荷分散に使用することはできません。詳細については、「[Azure の Traffic Manager による Azure の Web Sites のトラフィックの制御][trafficmanager]」を参照してください。
 
<a name="bkmk_configsharedmode"></a><h2>Web サイトの共有または標準モード用の構成</h2>

Web サイトのカスタム ドメイン名は、Azure の Web サイトの共有モードと標準モードに限って設定できます。Web サイトを無料 Web サイト モードから共有または標準 Web サイト モードに切り替える場合、最初に Web サイト サブスクリプションに設定されている使用制限を解除する必要があります。共有モードと標準モードの料金の詳細については、「[料金の詳細][PricingDetails]」を参照してください。

1. ブラウザーで、[管理ポータル][portal]を開きます。
2. **[Web Sites]** タブで、サイトの名前をクリックします。

	![][standardmode1]

3. **[規模の設定]** タブをクリックします。

	![][standardmode2]

	
4. **[全般]** セクションで、**[共有]** をクリックして Web サイト モードを設定します。

	![][standardmode3]

	> [WACOM.NOTE] この Web サイトで Traffic Manager を使用する場合は、共有モードの代わりに標準モードを使用する必要があります。

5. **[保存]** をクリックします。
6. 共有モードの追加料金についてのメッセージが表示された場合 (または標準モードでも同様に)、これに同意するときは **[はい]** をクリックします。

	<!--![][standardmode4]-->

	**メモ**<br />
	"Web サイト 'Web サイト名' のスケールの構成に失敗しました" というエラーが発生する場合は、詳細ボタンを使用して詳細情報を表示できます。

<a name="trafficmanager"></a><h2>(省略可能) Web サイトの Traffic Manager への追加</h2>

Traffic Manager で Web サイトを使用する場合は、次の手順を実行します。

1. Traffic Manager のプロファイルをまだ作成していない場合は、「[簡易作成による Traffic Manager プロファイルの作成][createprofile]」の手順に従って作成します。Traffic Manager プロファイルに関連付けられている **.trafficmgr.com** ドメイン名に注意してください。これは、後の手順で使用します。

2. Traffic Manager のプロファイルに Web サイトをエンドポイントとして追加する方法については、「[エンドポイントの追加と削除][addendpoint]」を参照してください。

	> [WACOM.NOTE] エンドポイントを追加する際に Web サイトが表示されない場合は、Web サイトが標準モードに構成されていることを確認します。Web サイトを Traffic Manager で操作するには、標準モードを使用する必要があります。

3. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

4. ここで CNAME レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。**CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

5. また、CNAME のドメインまたはサブドメイン エイリアスを指定する必要があります。たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。

5. また、この CNAME エイリアスの正規のドメイン名であるホスト名を指定する必要があります。これは Web サイトの **.trafficmgr.com** の名前です。

たとえば、次の CNAME レコードでは、**www.contoso.com** からのすべてのトラフィックが、Web サイトのドメイン名である **contoso.trafficmgr.com** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
<td><strong>正規のドメイン</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.trafficmgr.com</td>
</tr>
</table>

**www.contoso.com** の訪問者が本当のホスト (contoso.azurewebsite.net) を識別することはないため、転送プロセスはエンド ユーザーから見えなくなります。

> [WACOM.NOTE] Web サイトで Traffic Manager を使用している場合は、次のセクション「**カスタム ドメインの CNAME レコードの追加**」および「**カスタム ドメインの A レコードの追加**」の手順に従う必要はありません。前の手順で作成された CNAME レコードは、着信トラフィックを Traffic Manager にルーティングし、これは Web サイト エンドポイントにルーティングされます。

<a name="bkmk_configurecname"></a><h2>カスタム ドメインの CNAME レコードの追加</h2>

CNAME レコードを作成するには、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルに新しいエントリを追加する必要があります。それぞれのレジストラーでは CNAME レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。

1. これらの手段のいずれかを使用して、Web サイトに割り当てられた **.azurewebsite.net** ドメイン名を見つけます。

	* [Azure の管理ポータル][portal]にログインし、Web サイトを選択して、**[ダッシュボード]** を選択し、**[概要]** セクションの **[サイトの URL]** エントリを探します。

	* [Azure Powershell](http://www.windowsazure.com/ja-jp/manage/install-and-configure-windows-powershell/) をインストールして構成し、次のコマンドを使用します。

			get-azurewebsite yoursitename | select hostnames

	* [Azure Cross-Platform Command Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイス)](http://www.windowsazure.com/ja-jp/manage/install-and-configure-cli/) をインストールして構成し、次のコマンドを使用します。

			azure site domain list yoursitename

	次のステップで使用するため、この **.azurewebsite.net** という名前を保存します。

3. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

4. ここで CNAME レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。**CNAME**、**エイリアス**、または **サブドメイン**という単語を探します。

5. また、CNAME のドメインまたはサブドメイン エイリアスを指定する必要があります。たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。ルート ドメインのエイリアスを作成する場合は、レジストラーの DNS ツールに '**@**' シンボルとして示される場合があります。

5. また、この CNAME エイリアスの正規のドメイン名であるホスト名を指定する必要があります。これは Web サイトの **.azurewebsite.net** の名前です。

たとえば、次の CNAME レコードでは、**www.contoso.com** からのすべてのトラフィックが Web サイトのドメイン名である **contoso.azurewebsite.net** に転送されます。

<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
<tr>
<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
<td><strong>正規のドメイン</strong></td>
</tr>
<tr>
<td>www</td>
<td>contoso.azurewebsite.net</td>
</tr>
</table>

**www.contoso.com** の訪問者が本当のホスト
(contoso.azurewebsite.net) を識別することはないため、転送プロセスは
エンド ユーザーから見えなくなります。

> [WACOM.NOTE] 上の例は、__www__ サブドメインのトラフィックのみに該当します。CNAME レコードにはワイルドカードを使用できないため、各ドメインおよびサブドメインに 1 つの CNAME を作成する必要があります。サブドメイン (*.contoso.com など) からトラフィックを azurewebsite.net アドレスにダイレクトするには、DNS 設定の __URL リダイレクト__ エントリまたは __URL 転送__エントリを構成するか、または A レコードを作成します。

> [WACOM.NOTE]CNAME が DNS に反映されるまで多少の時間がかかります。CNAME が反映されるまで、Web サイトに対して CNAME を設定することはできません。<a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> などのサービスを使用すると、CNAME を利用できるかどうかを確認できます。

###Web サイトへのドメイン名の追加

ドメイン名の CNAME レコードが反映されたら、それを Web サイトに関連付ける必要があります。Azure クロス プラットフォーム コマンド ライン インターフェイスまたは Azure の管理ポータルを使用して、CNAME レコードで定義されたカスタム ドメイン名を Web サイトに追加することができます。

**コマンド ライン ツールを使用してドメイン名を追加するには**

[Azure Cross-Platform Command-Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイス)](http://www.windowsazure.com/ja-jp/manage/install-and-configure-cli/) をインストールして構成し、次のコマンドを使用します。

	azure site domain add customdomain yoursitename

たとえば、次では、**www.contoso.com** のカスタム ドメイン名が **contoso.azurewebsite.net** Web サイトに追加されます。

	azure site domain add www.contoso.com contoso

次のコマンドを使用してカスタム ドメイン名が Web サイトに追加されたことを確認できます。

	azure site domain list yoursitename

このコマンドで返される一覧には、カスタム ドメイン名、および既定の **.azurewebsite.net** エントリが含まれます。

**Azure の管理ポータルを使用してドメイン名を追加するには**

1. ブラウザーで、[Azure 管理ポータル][portal]を開きます。

2. **[Web Sites]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、ページの下部にある **[ドメインの管理]** を選択します。

	![][setcname2]

6. **[ドメイン名]** ボックスに、構成したドメインの名前を入力します。

	![][setcname3]

6. チェック マークをクリックしてドメイン名を受け入れます。

構成が完了したら、カスタム ドメイン名は Web サイトの **[構成]** ページの **[ドメイン名]** セクションに表示されます。

<a name="bkmk_configurearecord"></a><h2>カスタム ドメインの A レコードの追加</h2>

A レコードを作成するには、まず、Web サイトの IP アドレスを見つける必要があります。次に、レジストラーから提供されるツールを使用して、カスタム ドメイン用の DNS テーブルにエントリを追加します。それぞれのレジストラーでは A レコードを指定するための同様の (多少異なる) 手段が用意されていますが、その概念は同じです。また、A レコードの作成に加え、A レコードを確認するために使用される CNAME レコードを作成する必要があります。

1. ブラウザーで、[Azure 管理ポータル][portal]を開きます。

2. **[Web Sites]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、画面の下部にある **[ドメインの管理]** を選択します。

	![][setcname2]

5. **[カスタム ドメインを管理する]** ダイアログで、**[A レコードの構成時に使用する IP アドレス]** を探します。IP アドレスをコピーします。これは、A レコードの作成時に使用されます。

5. **[カスタム ドメインを管理する]** ダイアログで、ダイアログ上部のテキストの最後の awverify ドメイン名をメモします。**mysite** が Web サイトの名前である場合、これは **awverify.mysite.azurewebsites.net** となります。検証 CNAME レコードを作成する場合に使用されるドメイン名であるため、これをコピーします。

6. DNS レジストラーの Web サイトにログオンし、DNS の管理ページに移動します。**[ドメイン名]**、**[DNS]**、**[ネーム サーバー管理]** というラベルが付いたサイトのリンクまたは領域を探します。

6. A レコードと CNAME レコードを選択または入力する場所を探します。一覧からレコードの種類を選択するか、詳細設定ページに進まなければならない場合があります。

7. A レコードを作成するには、次のステップを実行します。

	1. A レコードを使用するドメインまたはサブドメインを選択または入力します。たとえば、**www.customdomain.com** のエイリアスを作成する場合は **www** を選択します。すべてのサブドメインのワイルドカード エントリを作成する場合は、'__*__' を入力します。これは、**mail.customdomain.com**、**login.customdomain.com**、**www.customdomain.com** などすべてのサブドメインを対象とします。

		ルート ドメインに A レコードを作成する場合は、レジストラーの DNS ツールに **@**' シンボルとして示される場合があります。

	2. 表示されたフィールドのクラウド サービスの IP アドレスを入力します。これによって、A レコードで使用されるドメイン エントリがクラウド サービスの展開の IP アドレスに関連付けられます。

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

		この例では、ルート ドメインの A レコードを作成する方法を示します。すべてのサブドメインを対象とするワイルドカードを作成する場合は、サブドメインとして '__*__' を入力します。

7. 次に、**awverify** のエイリアスがある CNAME レコード、および先に取得した **awverify.mysite.azurewebsites.net** の正規のドメインを作成します。

	> [WACOM.NOTE] レジストラーで使用される awverify のエイリアスもあれば、awverify.www.customdomainname.com または awverify.customdomainname.com の完全エイリアス ドメイン名を必要とするものもあります。

	たとえば、次は A レコード構成を確認するために使用する CNAME レコードを作成します。

	<table border="1" cellspacing="0" cellpadding="5" style="border: 1px solid #000000;">
	<tr>
	<td><strong>エイリアス/ホスト名/サブドメイン</strong></td>
	<td><strong>正規のドメイン</strong></td>
	</tr>
	<tr>
	<td>awverify</td>
	<td>awverify.contoso.azurewebsites.net</td>
	</tr>
	</table>

> [WACOM.NOTE] awverify CNAME が DNS に反映されるまで多少の時間がかかります。awverify CNAME が反映されるまで、Web サイトに対して A レコードで定義されたカスタム ドメイン名を設定することはできません。<a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> などのサービスを使用すると、CNAME を利用できるかどうかを確認できます。

###Web サイトへのドメイン名の追加

ドメイン名の **awverify** CNAME レコードが反映されたら、A レコードで定義されたカスタム ドメインを Web サイトに関連付けることができます。Azure クロス プラットフォーム コマンド ライン インターフェイスまたは Azure の管理ポータルを使用して、A レコードで定義されたカスタム ドメイン名を Web サイトに追加することができます。

**コマンド ライン ツールを使用してドメイン名を追加するには**

[Azure Cross-Platform Command-Line Interface (Azure クロス プラットフォーム コマンド ライン インターフェイス)](http://www.windowsazure.com/ja-jp/manage/install-and-configure-cli/) をインストールして構成し、次のコマンドを使用します。

	azure site domain add customdomain yoursitename

たとえば、次では、**contoso.com** のカスタム ドメイン名が **contoso.azurewebsite.net** Web サイトに追加されます。

	azure site domain add contoso.com contoso

次のコマンドを使用してカスタム ドメイン名が Web サイトに追加されたことを確認できます。

	azure site domain list yoursitename

このコマンドで返される一覧には、カスタム ドメイン名、および既定の **.azurewebsite.net** エントリが含まれます。

**Azure の管理ポータルを使用してドメイン名を追加するには**

1. ブラウザーで、[Azure 管理ポータル][portal]を開きます。

2. **[Web Sites]** タブで、サイト名をクリックし、**[ダッシュボード]** を選択して、ページの下部にある **[ドメインの管理]** を選択します。

	![][setcname2]

6. **[ドメイン名]** ボックスに、構成したドメインの名前を入力します。

	![][setcname3]

6. チェック マークをクリックしてドメイン名を受け入れます。

構成が完了したら、カスタム ドメイン名は Web サイトの **[構成]** ページの **[ドメイン名]** セクションに表示されます。

> [WACOM.NOTE] A レコードで定義されたカスタム ドメイン名を Web サイトに追加したら、レジストラーから提供されるツールを使用して awverify CNAME レコードを削除できます。ただし、今後、別の A レコードを追加するには、新しい A レコードで定義された新しいドメイン名を Web サイトに関連付ける前に awverify レコードを再作成する必要があります。

## 次の手順

-   [Web サイトの管理方法](http://www.windowsazure.com/ja-jp/manage/services/web-sites/how-to-manage-websites/)

-   [Web サイトの SSL 証明書の構成](http://www.windowsazure.com/ja-jp/develop/net/common-tasks/enable-ssl-web-site/)


<!-- Bookmarks -->

[Web サイトを共有モード用に構成する]: #bkmk_configsharedmode
[ドメイン レジストラーで CNAME を構成する]: #bkmk_configurecname
[ドメイン レジストラーで CNAME 検証レコードを構成する]: #bkmk_configurecname
[ドメイン名用に A レコードを構成する]:#bkmk_configurearecord
[管理ポータルでドメイン名を設定する]: #bkmk_setcname

<!-- Links -->

[PricingDetails]: https://www.windowsazure.com/ja-jp/pricing/details/
[portal]: http://manage.windowsazure.com
[digweb]: http://www.digwebinterface.com/
[cloudservicedns]: ../custom-dns/
[trafficmanager]: /ja-jp/documentation/articles/web-sites-traffic-manager/
[addendpoint]: http://msdn.microsoft.com/ja-jp/library/windowsazure/hh744839.aspx
[createprofile]: http://msdn.microsoft.com/ja-jp/library/windowsazure/dn339012.aspx

<!-- images -->

[setcname1]: ../media/dncmntask-cname-5.png


<!-- images -->
[standardmode1]: ./media/custom-dns-web-site/dncmntask-cname-1.png
[standardmode2]: ./media/custom-dns-web-site/dncmntask-cname-2.png
[standardmode3]: ./media/custom-dns-web-site/dncmntask-cname-3.png
[standardmode4]: ./media/custom-dns-web-site/dncmntask-cname-4.png 


[setcname2]: ./media/custom-dns-web-site/dncmntask-cname-6.png
[setcname3]: ./media/custom-dns-web-site/dncmntask-cname-7.png

