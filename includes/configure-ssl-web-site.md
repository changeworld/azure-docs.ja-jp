#Azure の Web サイトでの HTTPS の有効化

訪問者が HTTPS を使用して Web サイトにアクセスしてきた場合は、Web サイトとブラウザーの間の通信は Secure Socket Layer (SSL) 暗号化を使用してセキュリティで保護されます。この SSL 暗号化は、インターネットを介して送信されるデータをセキュリティで保護する際に最もよく使用される方法で、またサイトの訪問者に対し、そのサイト上でのトランザクションの安全性を保証します。この記事では、Azure の Web サイトに対して SSL を有効にする方法を説明します。

> [WACOM.NOTE]カスタム ドメイン名に対して SSL を有効にするには、標準モードで Web サイトを構成する必要があります。現在無料モードまたは共有モードを使用している場合、このモードで構成を行うと追加料金が発生する場合があります。共有モードと標準モードの料金の詳細については、「[料金の詳細][pricing]」を参照してください。

<a href="bkmk_azurewebsites"></a><h2>\*.azurewebsites.net ドメイン</h2>

カスタム ドメイン名を使用する計画がなく、代わりに Azure によってサイトに割り当てられた \*.azurewebsites.net ドメイン (たとえば、contoso.azurewebsites.net) を使用することを計画している場合は、サイトは既に、Microsoft から提供される証明書によってセキュリティで保護されています。**https://mywebsite.azurewebsites.net** を使用して Web サイトに安全にアクセスすることができます。

このドキュメントの残りの部分では、**contoso.com**、**www.contoso.com**、または **\*.contoso.com** のようなカスタム ドメイン名に対して HTTPS を有効にする方法を詳細に説明します。

<a href="bkmk_domainname"></a><h2>カスタム ドメイン名</h2>

**contoso.com** のようなカスタム ドメイン名に対して HTTPS を有効にするには、ドメイン名レジストラーにカスタム ドメイン名を登録する必要があります。Azure の Web サイトに対応するカスタム ドメイン名を構成する方法の詳細については、[Azure の Web サイトに対応するカスタム ドメイン名の構成に関するページ](/ja-jp/develop/net/common-tasks/custom-dns-web-site/) を参照してください。カスタム ドメイン名を登録し、そのカスタム名に応答するように Web サイトを構成した後、そのドメインに対応する SSL 証明書を要求する必要があります。

また、ドメイン名を登録した後は、**www.contoso.com** や **mail.contoso.com** のようなサブドメインを作成することもできます。SSL 証明書を要求する前に、その証明書により、どのドメイン名をセキュリティで保護するかを最初に決定する必要があります。この結果、どのような種類の証明書を取得する必要があるかが決まります。**contoso.com** または **www.contoso.com** のように、ただ 1 つのドメイン名を保護する必要がある場合は、通常は基本的な証明書で十分です。**contoso.com**、**www.contoso.com**、および **mail.contoso.com** のように複数のドメイン名を保護する必要がある場合は、ワイルドカード証明書、つまりサブジェクト代替名 (subjectAltName、SAN) を使用した証明書が必要になります。

> [WACOM.NOTE] 証明書で指定されているドメイン名が、ブラウザーに入力されたドメイン名と一致しない場合、ほとんどのブラウザーでは警告が表示されます。たとえば、証明書には www.contoso.com のみが示されており、Internet Explorer でサイトにアクセスするときに使用されたドメイン名が login.contoso.com である場合、"この Web サイトで提示されたセキュリティ証明書は、別の Web サイトのアドレス用に発行されたものです。" という警告が表示されます。

**基本証明書**は、証明書の共通名 (CN) が、サイトにアクセスするときにクライアントが使用する特定のドメインやサブドメインに設定されている証明書です。たとえば、**www.contoso.com** となります。これらの証明書は、CN で指定された 1 つのドメイン名のみをセキュリティで保護します。

**ワイルドカード証明書**は、証明書の共通名 (CN) のサブドメイン レベルにワイルドカード文字 \* が含まれている証明書です。ワイルドカード文字により、この証明書では、特定のドメインについてサブドメインの単一レベルとの照合ができます。たとえば、**\*.contoso.com** に対応するワイルドカード証明書は、**www.contoso.com**、**payment.contoso.com**、**login.contoso.com** に対して有効です。ただし **test.login.contoso.com** に対しては有効ではありません。このドメインには、追加のサブドメイン レベルが含まれているためです。また **contoso.com** に対しても有効ではありません。これはルート ドメイン レベルであり、サブドメインではないためです。

ワイルドカード証明書は、Web サイトに対して自動的に作成される \*.azurewebsites.net というドメイン名を対象にして Microsoft から提供されます。

**subjectAltName** は、さまざまな値 (サブジェクト代替名) を証明書に関連付けることができる証明書拡張です。SSL 証明書の場合、この証明書拡張を使用すると、証明書が有効となる DNS 名を追加することができます。たとえば、subjectAltName を使用する証明書には **contoso.com** という CN を含めることができ、さらに **www.contoso.com**、**payment.contoso.com**、**test.login.contoso.com** という代替名 (**fabrikam.com** も可能) も含めることができます。このような証明書は、共通名や subjectAltName に指定されているすべてのドメイン名に対して有効です。

証明書では、ワイルドカードと subjectAltName の両方をサポートすることができます。

<a href="bkmk_getcert"></a><h2>証明書の取得</h2>

Azure の Web サイトで使用する SSL 証明書は、この目的で証明書を発行する、信頼されたサード パーティである証明機関 (CA) によって署名されている必要があります。まだ SSL 証明書がない場合は、SSL 証明書を販売する会社から取得する必要があります。証明機関の一覧については、Microsoft TechNet Wiki の [Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA) に関するページ][cas]を参照してください。

証明書は、Azure における SSL 証明書の次の要件を満たす必要があります。

* 証明書は秘密キーを含む必要があります。

* 証明書はキー交換のために作成され、Personal Information Exchange (.pfx) ファイルにエクスポートできる必要があります。

* 証明書の件名は Web サイトへのアクセスに使用されるドメインと一致する必要があります。この証明書で複数のドメインを扱う場合は、前に説明したように、ワイルドカードの値を使用するか、subjectAltName の値を指定する必要があります。

	* Azure の Web サイトのカスタム ドメイン名の構成の詳細については、「[Azure の Web サイトのカスタム ドメイン名の構成に関するページ][customdomain]を参照してください。
	
	> [WACOM.NOTE] azurewebsites.net ドメインに対応する証明書の取得や生成を試みないでください。

* 証明書では、2,048 ビット以上の暗号化を使用する必要があります。

> [WACOM.NOTE] プライベート CA サーバーから発行された証明書は、Azure の Web サイトではサポートされていません。

証明機関 (CA) から SSL 証明書を取得するには、CA に送信される証明書署名要求 (CSR) を生成する必要があります。その後 CA は、CSR を完了するために使用する証明書を返します。CSR を生成する一般的な 2 つの方法は、certmgr.exe または [OpenSSL][openssl] アプリケーションを使用することです。Certmgr.exe は Windows のみで使用でき、OpenSSL はほとんどのプラットフォームで使用できます。この 2 つのユーティリティを使用する手順は次のとおりです。

CA が **中間証明書** (チェーン証明書とも呼びます) を使用している場合は、中間証明書も取得する必要があります。中間証明書を使用すると、"チェーンされていない証明書" を使用する場合よりセキュリティが強化されると見なされるため、CA で一般的に使用されています。中間証明書は、多くの場合、CA の Web サイトから個別にダウンロードする形で提供されています。この記事では、Azure の Web サイトにアップロードする証明書に対して任意の中間証明書が確実にマージされるようにする手順について説明します。

> [WACOM.NOTE] どちらの手順でも、**共通名**の入力が必要です。複数のドメイン (www.contoso.com、sales.contoso.com) で使用するためのワイルドカード証明書を取得する場合、この値を \*.domainname (たとえば \*.contoso.com) とする必要があります。1 つのドメイン名に対応した証明書を取得する場合、この値は、Web サイトを参照するときにユーザーがブラウザーに入力する値と完全に一致する値にする必要があります。たとえば、www.contoso.com となります。
>
> ワイルドカード名 (\*.contoso.com など) とルート ドメイン名 (contoso.com など) の両方をサポートする必要がある場合、ワイルドカードを含んだサブジェクト代替名 (SAN) 証明書を使用できます。SubjectAltName 拡張機能を使用する証明書要求の作成例については、「[SubjectAltName 証明書](#bkmk_subjectaltname)」を参照してください。
> > Azure の Web サイトのカスタム ドメイン名の構成方法の詳細については、<a href="/ja-jp/develop/net/common-tasks/custom-dns-web-site/">Azure の Web サイトのカスタム ドメイン名の構成に関するページ</a>を参照してください。

###Certreq.exe を使用した証明書の取得 （Windows のみ）

Certreq.exe は、証明書の要求を作成するための Windows ユーティリティです。Windows XP または Windows Server 2000 以降の Windows 基本インストールの一部であり、最近の Windows システムで使用できます。Certreq.exe を使用して SSL 証明書を取得するには、次のステップを使用します。

テスト用の自己署名証明書を作成する場合は、このドキュメントの[自己署名証明書](#bkmk_selfsigned)のセクションを参照してください。

IIS マネージャーを使用して証明書の要求を作成する場合は、[IIS マネージャーを使用した証明書の取得](#bkmk_iismgr)のセクションを参照してください。

1. **メモ帳**を開き、次の内容を含む新しい文書を作成します。Subject 行の **mysite.com** を、Web サイトに対応するカスタム ドメイン名で置き換えます。たとえば、Subject = "CN=www.contoso.com" と記述します。

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	上記で指定したオプションの詳細、および他の使用可能なオプションについては、[Certreq リファレンス ドキュメントに関するページ](http://technet.microsoft.com/library/cc725793.aspx).を参照してください。

2. テキスト ファイル **myrequest.txt** という名前で保存します。

3. **スタート画面**または **[スタート] メニュー**で、**cmd.exe** を実行します。

4. コマンド プロンプトで次のコマンドを使用して、証明書要求ファイルを作成します。

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	ステップ 1 で作成した **myrequest.txt** ファイルに対応するパスと、**myrequest.csr** ファイルを作成するときに使用するパスを指定します。

5. SSL 証明書を取得するために、**myrequest.csr** を証明機関に送信します。この送信には、ファイルをアップロードすること、またはメモ帳でファイルを開き、Web フォームに内容を貼り付ける作業が含まれる可能性があります。

	証明機関の一覧については、Microsoft TechNet Wiki の [Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA) に関するページ][cas]を参照してください。

6. 証明機関から証明書 (.CER) ファイルが提供された後、要求を生成したときに使用しコンピューターにこのファイルを保存してから、次のコマンドを使用して要求を受け入れ、証明書の生成プロセスを完了します。

		certreq -accept -user mycert.cer

	この例では、証明機関から受け取った **mycert.cer** 証明書を使用して、証明書の署名を完了します。ファイルは作成されません。代わりに、証明書が Windows 証明書ストアに格納されます。

6. CA が中間証明書を使用している場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。Microsoft IIS 用に提供されているバージョンを選択します。

	証明書をダウンロードした後、エクスプローラーで証明書を右クリックし、**[証明書のインストール]** をクリックします。**証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次]**  のクリックを続けます。

7. 証明書ストアから証明書をエクスポートするには、**スタート画面**または **[スタート] メニュー**から **certmgr.msc** を実行します。**証明書マネージャー**が表示された時点で、**[個人用]** フォルダーを展開し、**[証明書]** をクリックします。**[発行先]** フィールドで、証明書を要求したカスタム ドメイン名に対応するエントリを見つけます。**[発行者]** フィールドに、この証明書に関して使用した証明機関が表示されます。

	![証明書マネージャーに関するイメージをここに挿入します][certmgr]

9. 証明書を右クリックし、**[すべてのタスク]** をクリックしてから **[エクスポート]** をクリックします。**証明書のエクスポート ウィザード**で、**[次へ]** をクリックしてから **[はい、秘密キーをエクスポートします]** をクリックします。**[次へ]** をクリックします。

	![秘密キーをエクスポートします][certwiz1]

10. **[個人情報の交換 - PKCS #12]**、**[証明書チェーン内にすべての証明書を含める]**、および **[すべての拡張プロパティをエクスポートする]** を選択します。**[次へ]** をクリックします。

	![すべての証明書と拡張プロパティを含める][certwiz2]

11. **[パスワード]** をクリックし、パスワードの入力と確認入力を行います。**[次へ]** をクリックします。

	![パスワードの指定][certwiz3]

12. エクスポートした証明書を格納するファイル名とパスを指定します。ファイル名には、**.pfx** という拡張子を付ける必要があります。**[次へ]** をクリックしてプロセスを完了します。

	![ファイル パスを指定する][certwiz4]

ここで、エクスポートした PFX ファイルを Azure の Web サイトにアップロードすることができます。

###OpenSSL を使用した証明書の取得

1. コマンド ライン、bash、またはターミナル セッションから次を入力し、秘密キーと証明書署名要求を生成します:

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. メッセージが表示されたら、適切な情報を入力します。たとえば、

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		証明書の要求を送信するために、次の "追加" 属性を入力してください。

       	A challenge password []:

	この処理を完了すると、**myserver.key** および **server.csr** の 2 つのファイルが生成されます。**server.csr** には、証明書署名要求が含まれます。

3. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の [Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA)][cas] に関するページを参照してください。

4. CA からの証明書を入手した場合、**myserver.crt** という名前のファイルに保存します。CA がテキスト形式の証明書を提供した場合、**myserver.crt** ファイルに証明書のテキストを貼り付けるだけです。ファイルの内容をテキスト エディターで表示すると、次のようになります。

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	ファイルを保存します。

5. コマンド ライン、bash、またはターミナル セッションから、次のコマンドを使用して **myserver.key** および **myserver.crt** を Azure の Web サイトが必要とする形式である **myserver.pfx** に変換します。

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

	<div class="dev-callout"> 
	<b>メモ</b>
	<p>CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。PEM ファイル (ファイル拡張子は .pem) の形で提供されているバージョンを選択します。</p>
	<p>次のコマンドは、intermediate-cets.pem ファイルに格納されている中間証明書を含む .pfx ファイルを作成する方法を示しています。<b></b> file:</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	このコマンドを実行すると、Azure の Web サイトでの使用に適した **myserver.pfx** ファイルが生成されます。

<a href="bkmk_standardmode"></a><h2>標準モードの構成</h2>

カスタム ドメインに対して HTTPS を有効にできるのは、Azure の Web サイトで標準モードを使用している場合のみです。標準モードに切り替えるには、次のステップを使用します。

> [WACOM.NOTE] Web サイトを無料 Web サイト モードから標準モードに切り替える前に、Web サイト サブスクリプションに設定されている使用制限を解除する必要があります。この作業を実行しないと、請求期間が終了する前に制限に到達した場合に、サイトが使用できなくなるおそれがあります。共有モードと標準モードの料金の詳細については、「[料金の詳細][pricing]」を参照してください。

1. ブラウザーで、[管理ポータル][portal]を開きます。

2. **[Web サイト]** タブで、Web サイトの名前をクリックします。

	![Web サイトの選択][website]

3. **[規模の設定]** タブをクリックします。

	![スケール タブ][scale]

4. **[全般]** セクションで、**[標準]** をクリックして Web サイト モードを設定します。

	![標準モードの選択済み][standard]

5. **[保存]** をクリックします。メッセージが表示されたら、**[はい]** をクリックします。

	> [WACOM.NOTE] "Web サイト '&lt;サイト名&gt;' のスケールの構成に失敗しました" エラーが発生する場合は、詳細ボタンを使用して詳細情報を表示できます。"この要求を満たす、利用可能な標準インスタンス サーバーが足りません。" というエラーが発生する場合があります。このエラーが発生した場合は、[Azure サポート](http://www.windowsazure.com/ja-jp/support/options/)にお問い合わせください。

<a href="bkmk_configuressl"></a><h2>SSL の構成</h2>

このセクションの手順を実行する前に、Azure の Web サイトにカスタム ドメイン名が関連付けられている必要があります。詳細については、[Azure の Web サイトのカスタム ドメイン名の構成に関するページ][customdomain]を参照してください。

1. ブラウザーで、[Azure の管理ポータル][portal]を開きます。

2. **[Web サイト]** タブ上で、サイトの名前をクリックし、**[構成]** タブをクリックします。

	![構成タブ][configure]

3. **[証明書]** セクションで、**[証明書をアップロードします]** をクリックします。

	![証明書をアップロードします][uploadcert]

4. **[証明書をアップロードします]** ダイアログを使用して、IIS マネージャーまたは OpenSSL によって既に作成されている .pfx 証明書ファイルを選択します。.pfx ファイルをセキュリティ保護するために使用されたパスワードをある場合、それを指定します。最後に、**[チェック]** をクリックして証明書をアップロードします。

	![証明書のアップロード ダイアログ ボックス][uploadcertdlg]

5. **[構成]** タブの **[SSL のバインディング]** のセクションで、ドロップダウン リストから SSL でセキュリティ保護するドメイン名、および使用する証明書を選択します。さらに、[[Server Name Indication]][sni] (SNI)、または IP ベースの SSL のどちらを使用するかを選択できます。

	![SSL のバインディング][sslbindings]
	
	* IP ベースの SSL は、サーバーの専用パブリック IP アドレスをドメイン名にマッピングすることによって、証明書をドメイン名に関連付けします。これは、サービスに関連付けられている各ドメイン名 (contoso.com、fabricam.com など) の専用の IP アドレスが必要となります。これは SSL 証明書と Web サーバーを関連付ける従来の方式です。

	* SNI ベースの SSL は、SSL と[トランスポート層セキュリティ][tls] (TLS) の拡張です。TLS では、複数のドメインが同じ IP アドレスを共有し、各ドメインが独自のセキュリティ証明書を持つことができます。最新のブラウザー (Internet Explorer、Chrome、Firefox、および Opera を含む) のほとんどが SNI をサポートしていますが、古いブラウザーには、SNI をサポートしていないものもあります。SNI の詳細については、Wikipedia の [Server name Indication][sni] の記事を参照してください。

6. 変更を保存して SSL を有効にするには、**[保存]** をクリックします。

> [WACOM.NOTE] **IP ベースの SSL** を選択し、カスタム ドメインが A レコードを使用して構成されている場合、次の追加の手順を実行する必要があります。>
> 1. IP ベースの SSL バインドを構成すると、専用の IP アドレスが Web サイトに割り当てられます。この IP アドレスは、Web サイトの **[ダッシュボード]** ページの **[概要]** セクションで確認できます。このアドレスは、**仮想 IP アドレス**として示されます。
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>     この IP アドレスは、ドメイン用の A レコードを構成するために以前使用した仮想 IP アドレスとは異なります。SNI ベースの SSL を使用するように構成する場合、または SSL を使用するように構成しない場合は、このエントリに対してアドレスは表示されません。
>
> 2. ドメイン名レジストラーから提供されるツールを使用して、前の手順の IP アドレスを指定するようにカスタム ドメイン名用の A レコードを変更します。


ここで、証明書が正しく構成されていることを確認するために、HTTPS を使用して Web サイトを参照することができます。

<a href="bkmk_subjectaltname"></a><h2>SubjectAltName 証明書 (省略可能)</h2>

OpenSSL を使用して、1 つの証明書で複数のドメイン名をサポートするために SubjectAltName 拡張機能を使用する証明書要求を作成できます。ただし、その場合は構成ファイルが必要になります。次のステップでは、構成ファイルの作成と構成ファイルを使用した証明書の要求について説明します。

1. __sancert.cnf__ という名前の新しいファイルを作成し、このファイルの内容として次のコードを使用します。
 
		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default = 
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default = 
		localityName = Locality Name (eg, city)
		localityName_default = 
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  = 
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	"subjectAltName" で始まる行に注意してください。現在示されているドメイン名は、共通名に加えてサポートする必要があるドメイン名に置き換えてください。たとえば、

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	commonName_default フィールドを変更する必要はありません。次のステップのいずれかで、共通名を入力するように求められるためです。

2. __sancert.cnf__ ファイルを保存します。

1. sancert.cnf 構成ファイルを使用して、秘密キーと証明書署名要求を生成します。bash またはターミナル セッションから、次のコマンドを使用します。

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. メッセージが表示されたら、適切な情報を入力します。たとえば、

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	この処理を完了すると、**myserver.key** および **server.csr** の 2 つのファイルが生成されます。**server.csr** には、証明書署名要求が含まれます。

3. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の [Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA) に関するページ][cas]を参照してください。

4. CA からの証明書を入手した場合、**myserver.crt** という名前のファイルに保存します。CA がテキスト形式の証明書を提供した場合、**myserver.crt** ファイルに証明書のテキストを貼り付けるだけです。ファイルの内容をテキスト エディターで表示すると、次のようになります。

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	ファイルを保存します。

5. コマンド ライン、bash、またはターミナル セッションから、次のコマンドを使用して **myserver.key** および **myserver.crt** を Azure の Web サイトが必要とする形式である **myserver.pfx** に変換します。

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

	<div class="dev-callout"> 
	<b>メモ</b>
	<p>CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。PEM ファイル (ファイル拡張子は .pem) の形で提供されているバージョンを選択します。</p>
	<p>次のコマンドは、<b>intermediate-cets.pem</b> ファイルに格納されている中間証明書を含む .pfx ファイルを作成する方法を示しています。</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	このコマンドを実行すると、Azure の Web サイトでの使用に適した **myserver.pfx** ファイルが生成されます。

##<a name="bkmk_iismgr"></a>IIS マネージャーを使用した証明書の取得 (省略可能)

IIS マネージャーに慣れている場合は、IIS マネージャーを使用して、Azure の Web サイトで使用できる証明書を生成することができます。

1. IIS マネージャーで、証明機関に送信する証明書の署名要求 (CSR: Certificate Signing Request) を生成します。CSR 生成の詳細については、「[インターネット サーバー証明書を要求する (IIS 7)][iiscsr]」を参照してください。

2. SSL 証明書を取得するために、CSR を証明機関に送信します。証明機関の一覧については、Microsoft TechNet Wiki の [Windows および Windows Phone 8 SSL ルート証明書プログラム (メンバー CA) に関するページ][cas]を参照してください。

3. 証明機関ベンダーにより提供された証明書で CSR を完了します。CSR の完了手順については、「[インターネット サーバー証明書をインストールする (IIS 7)][installcertiis]」を参照してください。

4. CA が中間証明書を使用する場合、次のステップで証明書をエクスポートする前に、それらの中間証明書をインストールする必要があります。通常、これらの証明書は CA から個別のダウンロードとして提供されており、Web サーバーの種類に応じていくつかの形式で提供されています。Microsoft IIS 用に提供されているバージョンを選択します。

	証明書をダウンロードした後、エクスプローラーで証明書を右クリックし、**[証明書のインストール]** をクリックします。**証明書のインポート ウィザード**で既定値を使用し、インポートが完了するまで、**[次]**  のクリックを続けます。

4. IIS マネージャから証明書をエクスポートします。証明書のエクスポートの詳細については、「[サーバー証明書をエクスポートする (IIS 7)][exportcertiis]」を参照してください。エクスポートしたファイルは、Azure の Web サイトで使用するために、後の手順で Azure にアップロードする場合に使用されます。

	<div class="dev-callout"> 
	<b>メモ</b>
	<p>エクスポート プロセスでは、オプション [はい、秘密キーをエクスポートします]<strong></strong> を必ず選択してください。これにより、エクスポートされる証明書に秘密キーが含まれます。</p>
	</div>

	<div class="dev-callout"> 
	<b>メモ</b>
	<p>エクスポート プロセスでは、オプション [証明のパスにあるすべての証明書を含める]<strong></strong> と [すべての拡張プロパティをエクスポートする]<strong></strong> を必ず選択してください。これにより、エクスポートされる証明書にすべての中間証明書が含まれます。</p>
	</div>

<a href="bkmk_selfsigned"></a><h2>自己署名証明書 (省略可能)</h2>

テスト用の証明書を取得し、信頼された CA からの証明書の実際の購入は、運用開始時に行いたい場合もあります。この場合、自己署名証明書が有効です。自己署名証明書では、自身が証明機関 (CA) となり、証明書を作成および署名できます。この証明書は Web サイトのセキュリティ保護として使用できますが、証明書に信頼された CA の署名がないため、ほとんどのブラウザーはサイトの参照時にエラーを返します。ブラウザーによっては、サイトの表示を拒否することもあります。

自己署名証明書を作成する方法は複数ありますが、この記事では **makecert** および **OpenSSL** の使用に関する情報だけを扱います。

###自己署名証明書の作成makecert

Visual Studio がインストールされている Windows システムからテスト証明書を作成する手順は次のとおりです:

1. **[スタート] メニュー**または**スタート画面**で、「**開発者コマンド プロンプト**」を検索します。最後に、**[開発者コマンド プロンプト]** を右クリックし、**[管理者として実行]** をクリックします。

	[ユーザー アカウント制御] ダイアログ ボックスが表示された場合、**[はい]** をクリックして続行します。

2. [開発者のコマンド プロンプト] で、新しい自己署名証明書を作成するために次のコマンドを使用します。**serverdnsname** は、Web サイトの DNS に必ず置き換えてください。

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	このコマンドが作成する証明書は、2013 年 1 月 1 日から 2014 年 1 月 1 日まで有効で、CurrentUser の証明書ストア内の位置に格納されます。

3. **[スタート] メニュー**または**スタート画面**で、「**Windows PowerShell**」を検索し、このアプリケーションを開始します。

4. Windows PowerShell プロンプトで、以前に作成された証明書をエクスポートするために、次のコマンドを使用します:

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	これは $mypwd にセキュリティ保護された文字列として指定したパスワードを格納し、**dnsname** パラメーターによって指定された DNS 名を使用して証明書を検索し、**filepath** パラメーターに指定されたファイルにエクスポートします。パスワードを含むセキュリティ保護された文字列は、エクスポートしたファイルをセキュリティ保護するために使用されます。

###自己署名証明書の作成OpenSSL

1. **serverauth.cnf** という名前の新しいドキュメントを作成し、このファイルの内容として次を使用します:

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your website's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	これは、Azure の Web サイトで使用できる SSL 証明書の生成に必要な構成設定を指定します。

2. コマンド ライン、bash、またはターミナル セッションから次を入力し、新しい自己署名証明書を生成します:

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	これは **serverauth.cnf** ファイルで指定された構成設定を使用して、新しい証明書を作成します。

3. Azure の Web サイトにアップロードできる .PFX に証明書をエクスポートするには、次のコマンドを使用します:

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	メッセージが表示されたら、パスワードを入力して .pfx ファイルをセキュリティ保護します。

	このコマンドによって作成された **myserver.pfx** は、Azure の Web サイトをテスト目的でセキュリティ保護するために使用できます。

[customdomain]: /ja-jp/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/ja-jp/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/ja-jp/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/ja-jp/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/ja-jp/pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png


